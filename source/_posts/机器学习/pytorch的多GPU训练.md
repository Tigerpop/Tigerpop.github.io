---
layout: posts
title: pytorch的多GPU训练
date: 2024-11-7 16:58:30
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "机器学习"
tags:
- "torch"
- "GPU"
---



# 单机多卡

## DP（被淘汰）

**torch.nn.DataParallel**

- 简单一行代码 ，封装model即可。`model = DataParallel(model.cuda(),device_ids=[0,1,23] ) `;
- 模型保存与加载；由于被封装以后，model已经是一个DataParallel，所以 torch.save 调用 `model.module.state_dict()` ,`torch.load `注意 `map_location `;
- 缺点-（单进程，效率低）；
- 缺点- （不支持多机器）；
- 缺点-（不支持模型并行）；
- `batch_size `改成每个GPU `batch_size` 总和；



## DDP (推荐)

 **torch.nn.parallel.DistributedDataParallel **

- 多进程

- 初始化进程组` torch.distributed.init_process_group("nccl",world_size=n_gpus,rank=args.local_rank)`

- 指定当前进程使用哪一张 GPU 卡

   `torch.cuda.set_device(args.local_rank)` 相当于 CUDA_VISIBLE_DEVICES 环境变量

- 模型封装 
  (模型先转移到某一张 GPU卡上，然后再被ddp封装，注意是`device_ids`指定一张卡就好了，一个进程一张卡。) `model = DistributedDataParallel(model.cuda(args.local_rank),device_ids=[args.local_rank])`

- 将数据分布式分配到GPU上，给出一个训练顺序

  `train_sampler = DistributedSampler(train_dataset)`
  最好读一读 源码 `torch/util/data/distributed.py`

- 每个周期开始前，调用`train_sampler.set_epoch(epoch)` 打乱数据

- 有了 `sampler` 就不需要在 `DataLoader `设置`shuffle=True`

- 将` train_sampler `传入 `train_dataloader` 中

  `train_dataloader = DataLoader(...,sampler=train_sampler)`

- 数据拷贝到GPU卡

  `data = data.cuda(args.local_rank)`

- 执行命令

  (定一个节点上用几张卡)

  `python -m torch.distributed.launch --nproc_per_node=n_gpus train.py`

  `torch.distributed.launch` 会定多少个进程，根据前面传入的 节点内卡的数量,launch这个进程会向 每一个 train.py 传入  local_rank。

- 模型保存和加载

  `torch.save`在`local_rank=0` 的位置进行保存，同样注意调用` model.module.state_dict()`

  `torch.load`注意`map_location`

- 每一个进程所需要的 `batch_size` 应该是每一个 GPU所需要的 `batch_size`大小

  



# DDP例子

```python
import os
import argparse
import torch
import torch.nn as nn
import torch.optim as optim
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP
from torch.utils.data import Dataset, DataLoader
from torch.utils.data.distributed import DistributedSampler
from torchvision import transforms
# torch.distributed: 用于分布式训练的主要模块
# DistributedDataParallel: 用于模型的分布式封装
# DistributedSampler: 用于数据的分布式采样


# 这里创建了一个模拟数据集：
# 生成随机的3通道32x32图像
# 生成0-9之间的随机标签
class CustomDataset(Dataset):
    def __init__(self, size=1000):
        self.size = size
        self.data = torch.randn(size, 3, 32, 32)  # 模拟图像数据
        self.labels = torch.randint(0, 10, (size,))
    def __len__(self):
        return self.size
    def __getitem__(self, idx):
        return self.data[idx], self.labels[idx]

# 一个简单的卷积神经网络：
# 特征提取部分：两个卷积层，每个后面跟ReLU和池化
# 分类器部分：两个全连接层
class ConvNet(nn.Module):
    def __init__(self):
        super(ConvNet, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(2),
            nn.Conv2d(64, 128, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(2)
        )
        self.classifier = nn.Sequential(
            nn.Linear(128 * 8 * 8, 512),
            nn.ReLU(),
            nn.Linear(512, 10)
        )
    def forward(self, x):
        x = self.features(x)
        x = x.view(x.size(0), -1)
        x = self.classifier(x)
        return x

# 分布式设置函数:
def setup_distributed(args):
    # 可以通过环境变量设置 如果注释 就需要在 终端-m torch.distributed.launch 后指定端口号。
    # os.environ['MASTER_PORT'] = '29500'  
    
    # 初始化进程组
    torch.distributed.init_process_group(
        backend="nccl",         # 使用NCCL后端，专门优化用于GPU
        world_size=args.n_gpus, # 总GPU数量
        rank=args.local_rank    # 当前进程的rank
    ) 
    torch.cuda.set_device(args.local_rank) # 设置当前进程使用的GPU
    
def cleanup():
    dist.destroy_process_group()

# 模型保存函数:
def save_model(model, epoch, optimizer, args):
    if args.local_rank == 0:  # 只在主进程保存
        checkpoint = {
            'epoch': epoch,
            'model_state_dict': model.module.state_dict(),# 注意使用model.module获取原始模型参数
            'optimizer_state_dict': optimizer.state_dict(),# 保存优化器状态
        }
        save_path = f'checkpoint_epoch_{epoch}.pt'
        torch.save(checkpoint, save_path)
        print(f'Saved checkpoint to {save_path}')

# 模型加载函数:
def load_model(model, optimizer, checkpoint_path, args):
    map_location = {'cuda:%d' % 0: 'cuda:%d' % args.local_rank} # 保模型加载到正确的GPU
    checkpoint = torch.load(checkpoint_path, map_location=map_location)
    model.module.load_state_dict(checkpoint['model_state_dict']) # 加载模型状态
    optimizer.load_state_dict(checkpoint['optimizer_state_dict']) # 加载优化器状态
    return checkpoint['epoch']

def train(args):
    # 1. 设置分布式环境
    setup_distributed(args)
    
    # 2. 创建数据集和数据加载器
    train_dataset = CustomDataset()
    train_sampler = DistributedSampler(train_dataset)
    
    train_loader = DataLoader(
        train_dataset,
        batch_size=args.batch_size,  # 这里的batch_size是每个GPU的batch_size
        sampler=train_sampler,
        num_workers=4,
        pin_memory=True
    )
    
    # 3. 创建模型和优化器
    model = ConvNet()
    model = model.cuda(args.local_rank)  # 先移到对应GPU
    model = DDP(
        model,
        device_ids=[args.local_rank],
        output_device=args.local_rank
    )
    
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.Adam(model.parameters(), lr=0.001)
    
    # 4. 如果有检查点，加载模型
    start_epoch = 0
    if args.resume and os.path.exists(args.checkpoint):
        start_epoch = load_model(model, optimizer, args.checkpoint, args)
    
    # 5. 训练循环
    for epoch in range(start_epoch, args.epochs):
        train_sampler.set_epoch(epoch)  # 确保每个epoch数据被打乱
        
        model.train()
        for batch_idx, (data, target) in enumerate(train_loader):
            # 数据移到对应GPU
            data = data.cuda(args.local_rank, non_blocking=True)
            target = target.cuda(args.local_rank, non_blocking=True)
            
            # 前向传播
            output = model(data)
            loss = criterion(output, target)
            
            # 反向传播
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            
            # 只在主进程打印信息
            if batch_idx % args.print_freq == 0 and args.local_rank == 0:
                print(f'Epoch: {epoch}/{args.epochs}, '
                      f'Batch: {batch_idx}/{len(train_loader)}, '
                      f'Loss: {loss.item():.4f}')
        
        # 每个epoch结束后保存模型
        save_model(model, epoch, optimizer, args)
    
    cleanup()

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('--local_rank', type=int, default=-1)
    parser.add_argument('--n_gpus', type=int, default=4)
    parser.add_argument('--batch_size', type=int, default=32)
    parser.add_argument('--epochs', type=int, default=10)
    parser.add_argument('--print_freq', type=int, default=10)
    parser.add_argument('--resume', action='store_true')
    parser.add_argument('--checkpoint', type=str, default='')
    args = parser.parse_args()
    
    train(args)

if __name__ == '__main__':
    main()
```



在终端输入

```sh
python -m torch.distributed.launch \
    --nproc_per_node=4 \
    --master_port=29500 \
    train.py \
    --n_gpus=4 \
    --batch_size=32 \
    --epochs=10
```

> 找到 PyTorch 安装目录中的 distributed/launch.py
> 执行这个启动器脚本
> 启动器会创建多个进程来运行 train.py
> `-m` 表示将一个模块作为脚本运行，python a.py arg1 b.py arg2 c.py arg3 经常这样用。
>
> 命令行参数解释：
>
> - `--nproc_per_node=4`: 使用4个GPU
> - `--master_port=29500`: 指定主进程端口
> - `--n_gpus=4`: 总GPU数量
> - `--batch_size=32`: 每个GPU的batch size
> - `--epochs=10`: 训练轮数



# 一个我经常用的例子

这个是做 环境部署和GPU性能检测的。![ddp](pytorch%E7%9A%84%E5%A4%9AGPU%E8%AE%AD%E7%BB%83/ddp.jpg)

`ddp5_env_check.py`  

> 他会检测 驱动 torch conda等情况，并用此脚本文件夹中图片做多GPU测试。

```python
import os
import subprocess
import platform
import torch
import time
import torch.distributed as dist
import torch.multiprocessing as mp
from torch import nn
from torchvision import models, transforms
from PIL import Image
from torchvision.models import ResNet50_Weights
import glob
# from tqdm import tqdm
from torch.utils.data import Dataset, DataLoader, DistributedSampler
import argparse

# 检查并安装 tqdm
def install_tqdm():
    try:
        import tqdm
        print("tqdm 已安装，不需要重复安装。")
    except ModuleNotFoundError:
        print("tqdm 未安装，正在使用 pip 自动安装...")
        try:
            subprocess.check_call([f"{os.getenv('CONDA_PREFIX')}/bin/pip", "install", "tqdm", "-i", "https://pypi.tuna.tsinghua.edu.cn/simple"])
            import tqdm
            print("tqdm 安装成功！")
        except Exception as e:
            print(f"tqdm 安装失败: {e}")
            exit(1)

# 检查系统信息
def system_info():
    print("系统信息:")
    print(f"操作系统: {platform.system()} {platform.release()}")
    
    if platform.system() == 'Linux':
        try:
            with open("/etc/os-release") as f:
                for line in f:
                    if "PRETTY_NAME" in line:
                        ubuntu_version = line.split("=")[1].strip().strip('"')
                        print(f"Ubuntu 版本: {ubuntu_version}")
        except Exception as e:
            print(f"无法获取 Ubuntu 版本信息: {e}")
    else:
        print("Ubuntu 版本: N/A")
    
    print(f"Python 版本: {platform.python_version()}")
    print(f"Conda 虚拟环境: {os.getenv('CONDA_DEFAULT_ENV', 'Not in a conda environment')}")
    print(" ")

# 检查 PyTorch 和 CUDA 信息
def pytorch_cuda_info():
    print("PyTorch 和 CUDA 信息:")
    print(f"PyTorch 版本: {torch.__version__}")
    print(f"CUDA 可用性: {torch.cuda.is_available()}")
    
    if torch.cuda.is_available():
        print(f"CUDA 版本: {torch.version.cuda}")
        print(f"cuDNN 版本: {torch.backends.cudnn.version()}")
        gpu_count = torch.cuda.device_count()
        print(f"检测到 {gpu_count} 个 GPU")
        
        for i in range(gpu_count):
            print(f"GPU {i} 名称: {torch.cuda.get_device_name(i)}")
    else:
        print("CUDA 未启用，请检查 CUDA 安装")
    print(" ")

# 检查 PyTorch 是否调用了虚拟环境中的版本
def check_virtual_env():
    print("正在检查是否使用虚拟环境中的 PyTorch 版本...")
    conda_env = os.getenv('CONDA_DEFAULT_ENV', None)
    if conda_env:
        pytorch_path = torch.__file__
        print(f"PyTorch 安装路径: {pytorch_path}")
        if conda_env in pytorch_path:
            print(f"PyTorch 来自虚拟环境 '{conda_env}'")
        else:
            print("警告: PyTorch 没有来自当前虚拟环境")
    else:
        print("未在 Conda 虚拟环境中运行")
    print(" ")

class ImageDataset(Dataset):
    def __init__(self, img_paths, transform):
        self.img_paths = img_paths
        self.transform = transform

    def __len__(self):
        return len(self.img_paths)

    def __getitem__(self, idx):
        img_path = self.img_paths[idx]
        img = Image.open(img_path)
        if self.transform:
            img = self.transform(img)
        return img, img_path

def image_computation_test(local_rank, args):
    # 初始化进程组
    torch.cuda.set_device(local_rank)
    dist.init_process_group(backend="nccl", 
                          init_method=f"env://",
                          world_size=args.n_gpus,
                          rank=local_rank)
    
    print(f"Rank {local_rank}/{args.n_gpus} 开始工作...")
    
    # 初始化模型
    model = models.resnet50(weights=ResNet50_Weights.DEFAULT).cuda(local_rank)
    model = nn.parallel.DistributedDataParallel(model, device_ids=[local_rank])
    model.eval()

    # 图片预处理
    preprocess = transforms.Compose([
        transforms.Resize(256),
        transforms.CenterCrop(224),
        transforms.ToTensor(),
        transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
    ])

    # 创建数据集和数据加载器
    dataset = ImageDataset(args.img_paths, preprocess)
    sampler = DistributedSampler(dataset)
    dataloader = DataLoader(dataset, 
                          batch_size=args.batch_size,
                          sampler=sampler,
                          num_workers=args.num_workers,
                          pin_memory=True)

    start_time = time.time()

    for epoch in range(args.epochs):
        if local_rank == 0:
            print(f"\nEpoch {epoch+1}/{args.epochs}")
        
        sampler.set_epoch(epoch)  # 确保每个epoch的数据顺序不同
        
        for img_tensor, img_path in dataloader:
            img_tensor = img_tensor.cuda(local_rank)
            iterations = args.iterations
            success = True
            try:
                from tqdm import tqdm
                for i in tqdm(range(iterations), 
                            desc=f"Epoch {epoch+1}, 处理 {img_path[0]}", 
                            disable=local_rank!=0):
                    with torch.no_grad():
                        output = model(img_tensor)
            except Exception as e:
                print(f"运算失败: {e}")
                success = False

            if success:
                print(f"Rank {local_rank} 运算结束，处理了 {iterations} 次图片 '{img_path[0]}'")
            else:
                print(f"Rank {local_rank} 图片 '{img_path[0]}' 运算失败")

    elapsed = time.time() - start_time
    print(f"Rank {local_rank} 总耗时: {elapsed:.2f} 秒")
    print(" ")

    # 同步所有进程的时间
    elapsed_tensor = torch.tensor([elapsed], device=f"cuda:{local_rank}")
    gathered_times = [torch.zeros_like(elapsed_tensor) for _ in range(args.n_gpus)]
    dist.all_gather(gathered_times, elapsed_tensor)
    
    if local_rank == 0:
        total_time = sum(t.item() for t in gathered_times)
        print(f"\n并行计算耗时: {elapsed:.2f} 秒")
        print(f"所有 GPU 总共耗时: {total_time:.2f} 秒")
        for rank, time_val in enumerate(gathered_times):
            print(f"Rank {rank} 耗时: {time_val.item():.2f} 秒")
        print(f"\n总共实际耗时: {elapsed:.2f} 秒")

    dist.destroy_process_group()

def main():
    parser = argparse.ArgumentParser(description='多GPU分布式图像处理测试')
    
    # 分布式训练相关参数
    # parser.add_argument('--local_rank', type=int, default=-1,
    #                     help='DDP参数，由torch.distributed.launch自动传入')
    parser.add_argument('--n_gpus', type=int, default=1,
                        help='使用的GPU数量')
    
    # 数据加载相关参数
    parser.add_argument('--batch_size', type=int, default=1,
                        help='每个GPU的batch size')
    parser.add_argument('--num_workers', type=int, default=4,
                        help='数据加载的worker数量')
    
    # 训练相关参数
    parser.add_argument('--epochs', type=int, default=1,
                        help='运行的epoch数量')
    parser.add_argument('--iterations', type=int, default=8000,
                        help='每张图片的处理迭代次数')
    
    # 其他参数
    parser.add_argument('--seed', type=int, default=42,
                        help='随机种子')
    
    args = parser.parse_args()
    args.local_rank = int(os.environ["LOCAL_RANK"])   # 将环境变量中的 local_rank 添加到 args

    # 设置随机种子
    torch.manual_seed(args.seed)
    if torch.cuda.is_available():
        torch.cuda.manual_seed_all(args.seed)

    if args.local_rank == 0:
        install_tqdm()
        system_info()
        pytorch_cuda_info()
        check_virtual_env()

    if not torch.cuda.is_available():
        print("没有可用的 GPU，退出。")
        return

    if args.local_rank == 0:
        print(f'使用 GPU 数量: {args.n_gpus}')

    # 获取所有图片路径
    img_paths = glob.glob("*.jpg") + glob.glob("*.png")
    if not img_paths:
        print("没有找到任何图片，请检查路径。")
        return
    
    args.img_paths = img_paths

    # 运行计算测试
    image_computation_test(args.local_rank, args)

if __name__ == "__main__":
    main()

'''

【使用方法】
(淘汰)
python -m torch.distributed.launch \
    --nproc_per_node=4 \
    --master_port=29500 \
    script_name.py \
    --n_gpus=4 \
    --batch_size=32 \
    --epochs=10 \
    --num_workers=4 \
    --iterations=8000

torchrun \
    --nproc_per_node=4 \
    --master_port=29500 \
    script_name.py \
    --n_gpus=4 \
    --batch_size=32 \
    --epochs=10 \
    --num_workers=4 \
    --iterations=8000

实际上发生的是：

整个脚本会被启动4次（假设使用4个GPU）
每次启动都是完整的脚本
每个进程都有不同的 local_rank 值（0,1,2,3）
每个进程被分配到不同的 GPU

'''
```



`start_ddp.sh` 平时运行它、修改它就好了。

```sh
#!/bin/bash

# ---------------------------------------------
# 分布式训练启动脚本
# 
# 当使用 torch.distributed.launch 启动时：
# 1. 这个命令会自动启动n个进程(n=nproc_per_node指定的数量)
# 2. 每个进程都会运行同一个Python脚本(multi_gpu_process.py)
# 3. 每个进程会被自动分配一个local_rank(0,1,2,3...)
# 4. 每个进程会被自动分配到对应的GPU上
# ---------------------------------------------

torchrun \
   --nproc_per_node=2 \
   --master_port=29501 \
   ddp5_env_check.py \
   --n_gpus=2 \
   --batch_size=32 \
   --epochs=10 \
   --num_workers=4 \
   --iterations=800

# ---------------------------------------------
# 参数说明：
# --nproc_per_node: 要启动的进程数，通常等于GPU数量
# --master_port: 主进程的通信端口
# --n_gpus: 传递给Python脚本的GPU数量参数
# --batch_size: 每个GPU的批处理大小
# --epochs: 训练轮数
# --num_workers: 数据加载的工作进程数
# --iterations: 每张图片的处理迭代次数
# ---------------------------------------------
```

 以上 例子 需要说明的是 ddp 方式 来做 多进程的 GPU 操作，是不存在主进程的，实际是 用 rank0 所在的进程兼职了主进程，它一边运行着自己的GPU训练一边兼职了主进程的活。所以 我们上面写的 统计 总共用时的写法 是不准确的，事实上也只会 体现出 rank0的 运行时间，但是 我没有想看那么精确 也就没有改了。
