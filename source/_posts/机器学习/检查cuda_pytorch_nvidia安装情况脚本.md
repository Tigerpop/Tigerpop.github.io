---
layout: posts
title: 检查cuda_pytorch_nvidia安装情况脚本
date: 2024-10-25 11:12:13
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "机器学习"
tags:
- "cuda"
- "pytorch"
- "Nvidia驱动"




---



# 测试版本

```python
import os
import subprocess
import platform
import torch
import time
from torch import nn
from torchvision import models, transforms
from PIL import Image
from torchvision.models import ResNet50_Weights

# 检查并安装 tqdm
def install_tqdm():
    try:
        import tqdm
        print("tqdm 已安装，不需要重复安装。")
    except ModuleNotFoundError:
        print("tqdm 未安装，正在使用 pip 自动安装...")
        try:
            subprocess.check_call([f"{os.getenv('CONDA_PREFIX')}/bin/pip", "install", "tqdm", "-i", "https://pypi.tuna.tsinghua.edu.cn/simple"])
            import tqdm  # 确保安装成功
            print("tqdm 安装成功！")
        except Exception as e:
            print(f"tqdm 安装失败: {e}")
            exit(1)

# 检查系统信息
def system_info():
    print("系统信息:")
    print(f"操作系统: {platform.system()} {platform.release()}")
    
    # 获取 Ubuntu 版本
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
        print(f"GPU 名称: {torch.cuda.get_device_name(0)}")
    else:
        print("CUDA 未启用，请检查 CUDA 安装")
    print(" ")

# 测试 PyTorch 是否调用了虚拟环境中的版本
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

# 大量图片运算测试函数
def image_computation_test():
    print("开始大量图片运算测试...")
    device = "cuda" if torch.cuda.is_available() else "cpu"
    model = models.resnet50(weights=ResNet50_Weights.DEFAULT).to(device)
    model.eval()

    # 使用本地图片
    img_path = "picture_env_check.jpg"  # 本地图片的路径
    
    # 图片预处理
    preprocess = transforms.Compose([
        transforms.Resize(256),
        transforms.CenterCrop(224),
        transforms.ToTensor(),
        transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
    ])

    # 打开图片并处理
    if os.path.exists(img_path):
        img = Image.open(img_path)
        img_tensor = preprocess(img).unsqueeze(0).to(device)

        start_time = time.time()

        # 设置迭代次数模拟大量计算
        iterations = 8000  # 修改迭代次数以控制测试的规模
        success = True
        try:
            from tqdm import tqdm  # 在此处导入 tqdm，确保已经安装
            # 使用 tqdm 进度条
            for i in tqdm(range(iterations), desc="图片运算中"):
                with torch.no_grad():
                    output = model(img_tensor)
        except Exception as e:
            print(f"运算失败: {e}")
            success = False

        end_time = time.time()

        # 输出结果
        if success:
            print(f"运算结束，处理了 {iterations} 次图片，耗时: {end_time - start_time:.2f} 秒")
            print("运算成功！")
        else:
            print("运算失败")
    else:
        print(f"图片 '{img_path}' 未找到，请检查路径。")
    print(" ")

# 主函数
if __name__ == "__main__":
    install_tqdm()  # 安装 tqdm
    system_info()  # 输出系统信息
    pytorch_cuda_info()  # 输出 PyTorch 和 CUDA 信息
    check_virtual_env()  # 检查 PyTorch 是否来自虚拟环境
    image_computation_test()  # 运行大量图片运算测试
```



![image-20240929085301281](%E6%A3%80%E6%9F%A5cuda_pytorch_nvidia%E5%AE%89%E8%A3%85%E6%83%85%E5%86%B5%E8%84%9A%E6%9C%AC/image-20240929085301281.png)







# 通用版本

```python
import os
import subprocess
import platform
import torch
import time
from torch import nn
from torchvision import models, transforms
from PIL import Image
from torchvision.models import ResNet50_Weights

# 检查并安装 tqdm
def install_tqdm():
    try:
        import tqdm
        print("tqdm 已安装，不需要重复安装。")
    except ModuleNotFoundError:
        print("tqdm 未安装，正在使用 pip 自动安装...")
        try:
            subprocess.check_call([f"{os.getenv('CONDA_PREFIX')}/bin/pip", "install", "tqdm", "-i", "https://pypi.tuna.tsinghua.edu.cn/simple"])
            import tqdm  # 确保安装成功
            print("tqdm 安装成功！")
        except Exception as e:
            print(f"tqdm 安装失败: {e}")
            exit(1)

# 检查系统信息
def system_info():
    print("系统信息:")
    print(f"操作系统: {platform.system()} {platform.release()}")
    
    # 获取 Ubuntu 版本
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
        
        # 无论 GPU 数量如何，都打印 GPU 名称
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

# 大量图片运算测试函数
def image_computation_test():
    print("开始大量图片运算测试...")
    device = "cuda" if torch.cuda.is_available() else "cpu"
    
    # 检查 GPU 数量，使用多个 GPU
    gpu_count = torch.cuda.device_count()
    if gpu_count > 1:
        print(f"使用 {gpu_count} 个 GPU 进行并行运算")
        model = models.resnet50(weights=ResNet50_Weights.DEFAULT)
        model = nn.DataParallel(model)  # 使用多个 GPU
    else:
        print("只有一个 GPU，使用单 GPU 运算")
        model = models.resnet50(weights=ResNet50_Weights.DEFAULT)
    
    model = model.to(device)
    model.eval()

    # 使用本地图片
    img_path = "picture_env_check.jpg"  # 本地图片的路径
    
    # 图片预处理
    preprocess = transforms.Compose([
        transforms.Resize(256),
        transforms.CenterCrop(224),
        transforms.ToTensor(),
        transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
    ])

    # 打开图片并处理
    if os.path.exists(img_path):
        img = Image.open(img_path)
        img_tensor = preprocess(img).unsqueeze(0).to(device)

        start_time = time.time()

        # 设置迭代次数模拟大量计算
        iterations = 8000  # 修改迭代次数以控制测试的规模
        success = True
        try:
            from tqdm import tqdm  # 在此处导入 tqdm，确保已经安装
            # 使用 tqdm 进度条
            for i in tqdm(range(iterations), desc="图片运算中"):
                with torch.no_grad():
                    output = model(img_tensor)
        except Exception as e:
            print(f"运算失败: {e}")
            success = False

        end_time = time.time()

        # 输出结果
        if success:
            print(f"运算结束，处理了 {iterations} 次图片，耗时: {end_time - start_time:.2f} 秒")
            print("运算成功！")
        else:
            print("运算失败")
    else:
        print(f"图片 '{img_path}' 未找到，请检查路径。")
    print(" ")

# 主函数
if __name__ == "__main__":
    install_tqdm()  # 安装 tqdm
    system_info()  # 输出系统信息
    pytorch_cuda_info()  # 输出 PyTorch 和 CUDA 信息
    check_virtual_env()  # 检查 PyTorch 是否来自虚拟环境
    image_computation_test()  # 运行大量图片运算测试

```

## 改动总结：

- 在 `pytorch_cuda_info` 函数中，无论检测到多少 GPU 都会打印 GPU 名称。
- 其他部分代码保持不变，确保可以在多 GPU 或单 GPU 情况下正常运行。

以后如果要在其他脚本中加入类似的逻辑，只需在调用 GPU 相关的地方进行以下调整：

1. 使用 `torch.cuda.device_count()` 检查 GPU 数量。
2. 在需要多个 GPU 并行运算时，使用 `nn.DataParallel()` 包装模型。
3. 确保在任何情况下都打印出每个 GPU 的名称。





# 多GPU并行计算性能版本



## 一、pytorch下的GPU并行计算，举例两个标准方法

### 1. **`torch.multiprocessing` (mp)**

- **优点**：
  - 支持多进程并行，可以在多个 GPU 上并行处理不同的数据。
  - 每个进程有独立的 Python 解释器，这意味着每个进程可以独立地加载模型和数据，避免了全局解释器锁（GIL）的影响。
  - 可以利用 NVIDIA NCCL 后端实现高效的跨 GPU 通信。
- **缺点**：
  - 启动和管理多个进程的开销可能相对较高，特别是在小规模任务中。
  - 需要处理进程间的通信，可能会增加代码的复杂性。

### 2. **Data Parallelism数据并行 (ddp)**

- 使用 `torch.nn.DataParallel` 或 `torch.nn.parallel.DistributedDataParallel` 实现数据并行。
- **优点**：
  - `DataParallel` 在多个 GPU 上并行计算每个 mini-batch 的损失和梯度，使用简单。
  - `DistributedDataParallel` 更加高效，适合大规模分布式训练，减少了跨 GPU 的通信开销。
- **缺点**：
  - 在小型模型或小批量数据时，可能不会看到显著的性能提升。
  - 在某些情况下，`DataParallel` 的效率较低，因为它在主 GPU 上聚合梯度，可能成为瓶颈。



## 二、mp-精简逻辑

```python
import torch
import torch.distributed as dist
import torch.multiprocessing as mp

def setup_ddp(rank, world_size):
    os.environ['MASTER_ADDR'] = 'localhost'  # 在本机运行。
    os.environ['MASTER_PORT'] = '12355'      # 可以自己换端口，保证用一个空闲端口就行。
    dist.init_process_group("nccl", rank=rank, world_size=world_size)

def computation_function(rank, world_size):     
    setup_ddp(rank, world_size)
    # 这里可以放置模型训练或推理的逻辑
    print(f"Rank {rank} 正在执行计算逻辑。")
    # 其他计算逻辑...

if __name__ == "__main__":
    world_size = 4  # 假设有4个进程
    mp.spawn(computation_function, args=(world_size,), nprocs=world_size, join=True)
```

**`setup_ddp`**：设置分布式处理环境。

rank在多个方法中传递并标识，在的计算方法调用 setup_ddp 来进行环境设置，mp.spawn会自动生成一个rank到 计算方法中。这样就能确保每个进程都能正确地进行分布式计算。



```python
import os
import time
import torch
import torch.multiprocessing as mp
import torch.distributed as dist

def setup_ddp(rank, world_size):
    os.environ['MASTER_ADDR'] = 'localhost'
    os.environ['MASTER_PORT'] = '12355'
    dist.init_process_group("nccl", rank=rank, world_size=world_size)

def computation(rank, world_size, elapsed_times, img_paths):
    """具体计算函数，替换为所需的计算逻辑"""
    setup_ddp(rank, world_size)
    start_time = time.time()

    # 处理分配给该进程的图片
    for img_path in img_paths:
        # 这里添加处理逻辑，例如加载图片并进行计算
        pass  # 替换为你的计算逻辑

    elapsed_times[rank] = time.time() - start_time
    print(f"Rank {rank} 耗时: {elapsed_times[rank]:.2f} 秒")

def start_multiprocessing(computation_func, img_paths):
    """启动多进程并行计算"""
    world_size = torch.cuda.device_count()
    elapsed_times = mp.Manager().list([0] * world_size)

    # 任务划分
    images_per_rank = len(img_paths) // world_size
    extra_images = len(img_paths) % world_size
    image_lists = [img_paths[i * images_per_rank + min(i, extra_images):(i + 1) * images_per_rank + min(i + 1, extra_images)] for i in range(world_size)]

    start_time = time.time()
    mp.spawn(computation_func, args=(world_size, elapsed_times, image_lists), nprocs=world_size, join=True)
    end_time = time.time()

    total_time = sum(elapsed_times)
    print(f"总共实际耗时: {end_time - start_time:.2f} 秒")
    print(f"所有 GPU 总共耗时: {total_time:.2f} 秒")

if __name__ == "__main__":
    # 示例图片路径列表
    img_paths = ["image1.jpg", "image2.jpg", "image3.jpg", "image4.jpg"]  # 你可以根据需要填充图片路径
    start_multiprocessing(computation, img_paths)
```

- `mp.spawn` 是 PyTorch 的多进程模块提供的一个函数，用于方便地启动多个子进程（workers）。这个函数会为每个子进程调用指定的目标函数，并传递相应的参数。

**`args=(world_size, elapsed_times) + args`**:

- 这个参数定义了要传递给 `computation_func`

   的参数。它由几个部分组成：

  - **`world_size`**：当前可用的 GPU 数量，作为第一个参数传递给 `computation_func`。
  - **`elapsed_times`**：共享的列表，用于存储每个进程的计算耗时，也是作为第二个参数传递。
  - **`+ args`**：`args` 是一个可变参数列表，代表在调用 `start_multiprocessing` 时传递给函数的任何额外参数。这些参数会被附加到 `world_size` 和 `elapsed_times` 之后一起传递给 `computation_func`。

**`nprocs=world_size`**:

- 这个参数指定要启动的子进程数量。通常，`world_size` 是可用 GPU 的数量，所以这个参数表示我们希望在每个可用的 GPU 上启动一个进程，从而实现并行处理。

**`join=True`**:

- 这个参数控制 `mp.spawn` 的行为。当设置为 `True` 时，主进程会等待所有的子进程完成后再继续执行后续代码。如果设置为 `False`，主进程将不会等待子进程的完成。
- 一般来说，建议设置为 `True`，以确保主进程在所有子进程完成后再进行后续处理，比如计算总耗时等。



## 三、mp-可用代码

```python
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
from tqdm import tqdm

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

# 初始化 DDP
def setup_ddp(rank, world_size):
    os.environ['MASTER_ADDR'] = 'localhost'
    os.environ['MASTER_PORT'] = '12355'
    dist.init_process_group("nccl", rank=rank, world_size=world_size)

# 任务划分
def divide_images(img_paths, world_size):
    total_images = len(img_paths)
    base_count = total_images // world_size
    extra_count = total_images % world_size
    
    image_lists = []
    start_index = 0
    
    for i in range(world_size):
        # 每个 GPU 分配基本数量 + 可能的额外图片
        end_index = start_index + base_count + (1 if i < extra_count else 0)
        image_lists.append(img_paths[start_index:end_index])
        start_index = end_index
        
    return image_lists

# 大量图片运算测试函数
def image_computation_test(rank, world_size, img_paths, elapsed_times):
    setup_ddp(rank, world_size)
    print(f"Rank {rank}/{world_size} 开始工作...")
    device = f"cuda:{rank}"  # 每个进程使用不同的 GPU
    
    model = models.resnet50(weights=ResNet50_Weights.DEFAULT).to(device)
    model = nn.parallel.DistributedDataParallel(model, device_ids=[rank])
    model.eval()

    # 图片预处理
    preprocess = transforms.Compose([
        transforms.Resize(256),
        transforms.CenterCrop(224),
        transforms.ToTensor(),
        transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
    ])

    start_time = time.time()
    
    # 分配给当前 rank 的图片
    assigned_images = img_paths[rank]  # 从分配的图片列表中获取

    for img_path in assigned_images:
        if os.path.exists(img_path):
            img = Image.open(img_path)
            img_tensor = preprocess(img).unsqueeze(0).to(device)

            # 设置迭代次数模拟大量计算
            iterations = 8000
            success = True
            try:
                for i in tqdm(range(iterations), desc=f"处理 {img_path}"):
                    with torch.no_grad():
                        output = model(img_tensor)
            except Exception as e:
                print(f"运算失败: {e}")
                success = False

            if success:
                print(f"Rank {rank} 运算结束，处理了 {iterations} 次图片 '{img_path}'")
            else:
                print(f"Rank {rank} 图片 '{img_path}' 运算失败")
        else:
            print(f"图片 '{img_path}' 未找到，请检查路径。")
    
    elapsed = time.time() - start_time
    elapsed_times[rank] = elapsed
    print(f"Rank {rank} 总耗时: {elapsed:.2f} 秒")
    print(" ")

# 启动多进程
def start_multiprocessing():
    world_size = torch.cuda.device_count()
    if world_size < 1:
        print("没有可用的 GPU，退出。")
        return
    print(f'可用 GPU 数量: {world_size}')

    img_paths = glob.glob("*.jpg") + glob.glob("*.png")  # 获取所有图片
    if not img_paths:
        print("没有找到任何图片，请检查路径。")
        return
    
    # 将图片划分为每个进程的任务
    image_lists = divide_images(img_paths, world_size)
    
    # 创建一个共享列表来存储每个进程的耗时
    elapsed_times = mp.Manager().list([0] * world_size)
    
    start_time = time.time()  # 记录并行计算开始时间
    mp.spawn(image_computation_test, args=(world_size, image_lists, elapsed_times), nprocs=world_size, join=True)
    end_time = time.time()  # 记录并行计算结束时间

    total_time = sum(elapsed_times)
    parallel_elapsed_time = end_time - start_time  # 计算并行计算的耗时
    print(f"\n并行计算耗时: {parallel_elapsed_time:.2f} 秒，（若仅有一个GPU，仅看Rank耗时即可，此项统计可忽略。）")
    print(f"所有 GPU 总共耗时: {total_time:.2f} 秒")
    for rank, elapsed in enumerate(elapsed_times):
        print(f"Rank {rank} 耗时: {elapsed:.2f} 秒")

    total_elapsed_time = end_time - start_time
    print(f"\n总共实际耗时: {total_elapsed_time:.2f} 秒； （若仅有一个GPU，仅看Rank耗时即可，此项统计可忽略。）")

# 主函数
if __name__ == "__main__":
    install_tqdm()  # 安装 tqdm
    system_info()  # 输出系统信息
    pytorch_cuda_info()  # 输出 PyTorch 和 CUDA 信息
    check_virtual_env()  # 检查 PyTorch 是否来自虚拟环境
    
    start_multiprocessing()  # 启动多进程运算
```



## 四、ddp精简逻辑

以下是精简的 DDP 结构示例：

```python
import os
import torch
import torch.distributed as dist
import torch.multiprocessing as mp
from torch import nn
from torch.nn.parallel import DistributedDataParallel as DDP
from torch.utils.data import DataLoader, DistributedSampler, TensorDataset

# 初始化分布式训练环境
def setup(rank, world_size):
    os.environ['MASTER_ADDR'] = 'localhost'
    os.environ['MASTER_PORT'] = '12355'
    dist.init_process_group("nccl", rank=rank, world_size=world_size)

# 销毁进程组
def cleanup():
    dist.destroy_process_group()

# 定义模型（替换成你自己的模型）
def create_model():
    return nn.Linear(10, 2)  # 简单线性层示例

# 定义数据加载（替换成你自己的数据加载逻辑）
def create_dataloader(rank, world_size):
    data = torch.randn(1000, 10)  # 假设 1000 个样本，每个 10 个特征
    labels = torch.randint(0, 2, (1000,))  # 假设二分类
    dataset = TensorDataset(data, labels)
    sampler = DistributedSampler(dataset, num_replicas=world_size, rank=rank)
    return DataLoader(dataset, batch_size=32, sampler=sampler)

# 训练逻辑（替换成你自己的训练逻辑）
def train(rank, world_size):
    setup(rank, world_size)
    device = torch.device(f"cuda:{rank}")

    # 初始化模型和数据
    model = create_model().to(device)
    model = DDP(model, device_ids=[rank])
    dataloader = create_dataloader(rank, world_size)
    criterion = nn.CrossEntropyLoss()
    optimizer = torch.optim.SGD(model.parameters(), lr=0.01)

    # 训练循环
    for epoch in range(5):  # 假设 5 个训练轮次
        for inputs, labels in dataloader:
            inputs, labels = inputs.to(device), labels.to(device)
            outputs = model(inputs)
            loss = criterion(outputs, labels)

            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
        
        print(f"Rank {rank}, Epoch {epoch}, Loss: {loss.item():.4f}")
    cleanup()

# 启动多进程
def main():
    world_size = torch.cuda.device_count()
    mp.spawn(train, args=(world_size,), nprocs=world_size, join=True)

if __name__ == "__main__":
    main()
```

### 模块化的逻辑说明

1. **`setup` 和 `cleanup`**：用于初始化和销毁分布式进程组。
2. **`create_model`**：定义模型结构，直接替换成你自己的模型。
3. **`create_dataloader`**：创建数据加载器，使用 `DistributedSampler` 确保每个进程获得数据的不同部分。
4. **`train`**：封装训练逻辑。模型、数据、损失函数和优化器定义在此函数中。训练代码逻辑直接可以替换。

这个结构方便修改模型和数据加载逻辑，同时保留 DDP 的核心初始化和训练逻辑。



以下是 DDP 在代码中的核心环节，以及实现效果的关键代码讲解：

### 1. 初始化分布式进程 (`setup` 函数)

```python
def setup(rank, world_size):
    os.environ['MASTER_ADDR'] = 'localhost'      # 设置主节点地址
    os.environ['MASTER_PORT'] = '12355'          # 设置主节点端口
    dist.init_process_group("nccl", rank=rank, world_size=world_size)  # 初始化进程组
```

- **`MASTER_ADDR` 和 `MASTER_PORT`**：指定主进程的地址和端口，以便所有 GPU 进程能通过这个主节点进行通信。这里设置为本地通信（`localhost`），适合单机多卡情况。
- **`dist.init_process_group`**：初始化分布式进程组，指定使用 `"nccl"` 后端（针对 GPU 的高效通信框架）。这个过程会在每个 GPU 上创建一个进程，参与模型的同步。

> **作用**：在 DDP 中，必须在每个 GPU 进程上初始化一个分布式进程组。`nccl` 能更高效地处理 GPU 间通信。

### 2. 模型的分布式封装 (`DDP` 包装模型)

```python
device = torch.device(f"cuda:{rank}")
model = create_model().to(device)
model = DDP(model, device_ids=[rank])
```

- **`to(device)`**：将模型移到指定的 GPU 上，确保每个进程在特定的 GPU（由 `rank` 指定）上执行。
- **`DDP(model, device_ids=[rank])`**：通过 `DDP` 封装模型，以便在训练过程中自动处理梯度同步。每次反向传播后，DDP 会自动在每个 GPU 进程之间同步梯度。

> **作用**：封装后的模型会在每个 GPU 之间自动同步参数。每次 `backward()` 调用时，DDP 会收集所有 GPU 的梯度并计算平均值，再将平均后的梯度分发到每个 GPU，确保模型参数在所有设备上一致。

### 3. 数据并行化 (`DistributedSampler`)

```python
sampler = DistributedSampler(dataset, num_replicas=world_size, rank=rank)
dataloader = DataLoader(dataset, batch_size=32, sampler=sampler)
```

- **`DistributedSampler`**：数据加载器的采样器，确保每个 GPU 进程读取数据集的不同部分，避免数据重复。
- **`num_replicas=world_size`**：指定进程数量，保证数据集分配给所有进程。
- **`rank=rank`**：指定当前进程的 GPU ID，使每个 GPU 加载不同的样本。

> **作用**：确保每个进程处理数据集的不同部分，从而充分利用多 GPU 资源。同时避免多 GPU 处理相同数据的重复计算。

### 4. 自动梯度同步和参数更新

```python
for inputs, labels in dataloader:
    inputs, labels = inputs.to(device), labels.to(device)
    outputs = model(inputs)
    loss = criterion(outputs, labels)

    optimizer.zero_grad()
    loss.backward()   # 自动同步梯度
    optimizer.step()   # 参数更新
```

- **`loss.backward()`**：反向传播时，DDP 会自动将各 GPU 进程的梯度同步并计算平均值。每个 GPU 上的模型参数通过梯度更新保持一致。
- **`optimizer.step()`**：在所有进程中执行参数更新，确保下一步前参数保持同步。

> **作用**：在反向传播的同时完成梯度同步，确保各个 GPU 进程的模型更新一致，实现真正的分布式数据并行。

### 5. 结束分布式训练 (`cleanup`)

```python
def cleanup():
    dist.destroy_process_group()
```

- **`dist.destroy_process_group()`**：销毁进程组，释放 GPU 资源，结束分布式训练。

> **作用**：在训练完成后清理资源，避免 GPU 资源占用或潜在的内存泄漏。





# 一个我经常用的例子

这个是做 环境部署和GPU性能检测的。![ddp](%E6%A3%80%E6%9F%A5cuda_pytorch_nvidia%E5%AE%89%E8%A3%85%E6%83%85%E5%86%B5%E8%84%9A%E6%9C%AC/ddp.jpg)

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