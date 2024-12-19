---
layout: posts
title: GPU服务器软件笔记
date: 2024-10-30 14:40:24
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "机器学习"
tags:
- "cuda"
- "pytorch"
- "Nvidia驱动"
- "前向兼容-后向兼容-小版本兼容"



---



#  一、制作引导U盘

​		在ubuntu官网下载服务器版本的ubuntu【Ubuntu 20.04.6 LTS】https://releases.ubuntu.com/20.04/

![截屏2024-09-26 09.34.20](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.34.20.jpg)

​		然后使用Rufus或者其他工具，制作引导U盘。



# 二、bios配置

​		在bios中关闭 网络引导，然后打开快速引导和 USB设备引导，在boot选项中把USB优先级提高，或者直接选择USB设备boot；

![image-20240926094301173](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240926094301173.png)

​		如遇到 辅助风扇报错可暂时不处理；

![image-20240926093920689](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240926093920689.png)



# 三、安装ubuntu

​	![微信图片_20241120172957](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20241120172957.jpg)

​	配置的时候最好用AMT这个网口，Ubuntu 安装完毕以后 把网线再查回非AMT的网口，下面装Ubuntu的过程我们都是插在AMT口操作的。

选语言；

![截屏2024-09-26 09.46.28](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.46.28.jpg)

​		选键盘布局

![截屏2024-09-26 09.47.08](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.47.08.jpg)

​		选择手动还是DHCP动态分配IP；我这里选择的手动manual；

![image-20240926094918382](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240926094918382.png)

![image-20240926094947415](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240926094947415.png)

​		如果您的Ubuntu服务器在代理服务器后面，则需要在此处输入代理服务器详细信息。否则，只需将其保留为空；

![截屏2024-09-26 09.50.50](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.50.50.jpg)

​		需要配置Ubuntu存档镜像。系统会自动为你选择一个默认的镜像。如果您不希望使用默认镜像，请在此处输入“镜像地址”，这个可以配置阿里云(http://mirrors.aliyun.com/ubuntu/)，我这里使用默认的，但是不代表默认的每次都行，这里强烈建议改成清华的源：https://mirrors.tuna.tsinghua.edu.cn/ubuntu    ；

![截屏2024-09-26 09.52.06](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.52.06.jpg)

​		配置磁盘布局，即对硬盘进行分区。您将获得两个选择。选择任何适合您的给定分区方法。

​		使用整个磁盘：此方法将擦除整个硬盘并自动对驱动器进行分区。推荐给新手。如果选择此选项，则可以选择设置LVM并使用LUKS加密LVM配置。

![截屏2024-09-26 09.53.57](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.53.57.jpg)

![image-20240926095417330](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240926095417330.png)

​		continue之后 ，输入服务器系统的详细信息，例如您的名称，服务器名称，用户名及其密码，您将使用它们来登录服务器。（注意此时如果你设置了阿狗，那阿狗就具备root权限。）但是注意，此时 你想切root用户是做不到的，但是root用户又是实际存在的，要切root 用户，需要先`passwd root`给root用户定一个密码。
​		![image-20240926095714212](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240926095714212.png)

​		可以选择安装openSSH服务器，该服务器允许您从网络上的任何系统远程访问该服务器。
![截屏2024-09-26 09.58.34](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.58.34.jpg)

![截屏2024-09-26 09.59.01](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2009.59.01.jpg)

​		Ubuntu 20.04 LTS安装将立即开始。需要一段时间，具体取决于软件包的选择和Internet的速度。

![image-20240926100005353](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240926100005353.png)

​		reboot，立刻拔出U盘。

```sh
cat /etc/os-release # 查看版本是不是正确了。
```



# 四、查看GPU型号

```sh
 lspci | grep -i nvidia # 如果只出现了2204 这样的编号没有 出现 像 GTX4090类似这样的具体型号，那就自己去网上搜 编号和具体型号的对应，像2204 对应就是 GTX3090。
```

也能拆机看看 显卡上有没有贴着，直接明了就是拆机麻烦点。



# 五、安装Nvidia驱动



## 	五-0、兼容性知识的了解

​		兼容性推荐看这个解释
https://tianzhipeng-git.github.io/2023/11/21/cuda-version.html#%E5%8D%A1%E5%92%8Cdriver%E7%89%88%E6%9C%AC%E5%85%BC%E5%AE%B9

​		有几个点要理解 

- GPU kernal-mode driver

- CUDA user-mode driver

- CUDA runtime

  上面两个绑定在一起，在Nvidia驱动，cuda runtime 可以理解为 我们分析的cuda版本，nvidia驱动是向后兼容的，支持老版本，现在的Nvidia驱动对cuda 也有小版本兼容，如果Nvidia驱动最开始对应的12.0，则12.0 12.1 12.X都是兼容的。

  torch安装起来直接考虑所安装的torch附带的cuda版本 , 如`torch-1.12.0+cu116-cp39`, 和机器的driver是否兼容即可

  **另外需要强调的是，pytorch 自带了一个 cuda支持包 ，大部分时候不会调用 本地系统中的cuda，不需要安装本地的cuda服务**。

  

## 	五-1、下载

​		先进Nvidia官网看看这个显卡推荐的驱动，https://www.nvidia.com/en-us/drivers/    。
​		下面是Tesla V100GPU和GTX3090的例子；
​		![截屏2024-09-26 10.48.17](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2010.48.17.jpg)

​		![截屏2024-09-26 10.53.02](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2010.53.02.jpg)

​		![截屏2024-09-26 10.57.57](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-26%2010.57.57.jpg)

```sh
# 复制上图 Download 地址，在服务器中用wget下载。
wget https://us.download.nvidia.com/XFree86/Linux-x86_64/550.120/NVIDIA-Linux-x86_64-550.120.run
```



## 	五-2、安装

​		确保 nvidia驱动被 彻底卸载了，所以先尝试卸载一遍；

```sh

# ——————————————————————————————————————  准备工作  ————————————————————————————————————————
nvidia-smi # 查看是否有安装 Nvidia 驱动；
lspci | grep -i nvidia  # 确认是否识别到设备：


sudo systemctl stop lightdm
sudo systemctl stop gdm

# 确保禁用 Nouveau 驱动
sudo vim /etc/modprobe.d/blacklist-nouveau.conf
# 添加
blacklist nouveau
options nouveau modeset=0

# 更新 initramfs： 执行以下命令以更新 initramfs，以确保系统在下次启动时禁用 Nouveau：
sudo update-initramfs -u
sudo reboot 

# ——————————————————————————————————————  准备工作  ————————————————————————————————————————



# ——————————————————————————————————————  卸载  ————————————————————————————————————————
# 如果有安装Nvidia驱动，且可以确定是 run 文件安装的，则先额外添加以下一条命令，后面一样。
sudo bash NVIDIA-XXX-XXX.XXX.run --uninstall 
# 如果不是是 run 文件安装的，直接暴力全部卸载。
# 确保 nvidia驱动被有哪些。
sudo dpkg -l | grep nvidia  
sudo apt-get purge '^nvidia-.*'
    
    # ¥ 若遇到以下错误 
    Errors were encountered while processing:
     qemu-ga
    # ¥ 处理方法
    sudo apt-get remove --purge qemu-ga
    sudo apt-get autoremove
    sudo apt-get clean
    sudo apt-get update
    sudo apt-get install qemu-ga 或者 sudo apt-get install qemu-guest-agent
		sudo apt-get purge '^nvidia-.*'

sudo apt-get autoremove --purge
sudo apt-get clean
# 查看 卸载干净没有
sudo dpkg -l | grep nvidia  
sudo reboot
# ——————————————————————————————————————  卸载  ————————————————————————————————————————



# ——————————————————————————————————————  安装  ————————————————————————————————————————
		
		# apt换源，先备份
    sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
    sudo vim /etc/apt/sources.list
    # 写入
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse

	
	# 情况一：下载的是 deb 文件。
	sudo dpkg -i nvidia-driver-local-repo-ubuntu2004-550.54.14_1.0-1_amd64.deb
	sudo cp /var/nvidia-driver-local-repo-ubuntu2004-550.54.14/nvidia-driver-local-F9E6E988-keyring.gpg /usr/share/keyrings/
	sudo apt-get update
	sudo apt-get -f install
	sudo apt install nvidia-driver-550
	sudo reboot 
	nvidia-smi # 可见安装成功，但不一定是550.54.14版本，可能是550.90.12。
	
	
	# 情况二：下载的是 run 文件。
	sudo bash NVIXXX.XXX.XXX install 
	# 可能需要安装gcc make等
	sudo apt update
	sudo apt install gcc
  sudo apt install make
	
# ——————————————————————————————————————  安装  ————————————————————————————————————————

# 安装Nvidia驱动之后，不再安装系统的 cuda，而在 conda 中根据各自虚拟环境的的要求,安装对应的cuda 和pytorch即可。

```



## 	五-3、重启nvidia服务

```sh
sudo systemctl restart nvidia-persistenced

sudo reboot 
```



# 六、安装cuda

​		cuda 官网 https://developer.nvidia.com/cuda-12-4-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=deb_local

```sh
# ——————————————————————————————————————  卸载  ————————————————————————————————————————

sudo apt-get --purge remove "*cublas*" "cuda*" "nsight*"

sudo rm /etc/apt/sources.list.d/cuda*
sudo rm /etc/apt/sources.list.d/nvidia-ml.list

sudo apt-get autoremove
sudo apt-get autoclean

sudo reboot

# ——————————————————————————————————————  卸载  ————————————————————————————————————————
```

​		

```sh
# ——————————————————————————————————————  安装  ————————————————————————————————————————

# 把临时的管道文件夹设置在一个大点的磁盘内，这个文件夹挂着个大磁盘，并且这个是一次性的，下次开机就不生效了。
mkdir -p /data/tmp
export TMPDIR=/data/tmp


换源，先备份
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup

sudo vim /etc/apt/sources.list
# 写入
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse

### 官网内容 ###
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
    sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget https://developer.download.nvidia.com/compute/cuda/12.4.0/local_installers/cuda-repo-ubuntu2004-12-4-local_12.4.0-550.54.14-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu2004-12-4-local_12.4.0-550.54.14-1_amd64.deb
    sudo cp /var/cuda-repo-ubuntu2004-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/
    sudo apt-get update
    sudo apt-get -y install cuda-toolkit-12-4
### 官网内容 ###

# 在 .bashrc 写入
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH

source ~/.bashrc
nvcc --version

# ——————————————————————————————————————  安装  ————————————————————————————————————————
```



# 七、安装conda

​		下载conda，我推荐使用miniconda，https://docs.conda.io/en/latest/miniconda.html#linux-installers。



```sh
# 添加环境变量，然后再 source ~/.bashrc

```



​		兄弟改个国内源吧，不然贼慢。

```sh
cp ~/.condarc ~/.condarc.bak

vim  ~/.condarc

# 写入以下内容 
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
  - conda-forge
  - defaults  
```



使用 `pip` 命令安装 Jupyter Lab，同时确保安装在用户的本地目录中：

`pip install --user jupyterlab`

如果 `~/.local/bin` 没有看到，可以将其添加到 `~/.bashrc` 或 `~/.bash_profile` 中：

```sh
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```



# 八、虚拟环境内安装cuda及pytorch

conda update conda

注意：https://pytorch.org/get-started/locally/ 查看 cuda和pytorch对应关系，nvidia-smi 中右上角的 cuda是Nvidia驱动最高支持到的cuda版本，之后下载的cuda不能高于这个版本。![截屏2024-09-27 21.16.11](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/%E6%88%AA%E5%B1%8F2024-09-27%2021.16.11.jpg)![image-20240927211510343](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240927211510343.png)



```sh
#（重要）先把环境外的 pytorch都给卸载了，不然可能影响环境中调用 pytorch，可能调到外面的导致混乱。
pip uninstall torch torchvision torchaudio
conda remove pytorch torchvision torchaudio

# 进入 自己环境，安装cudatoolkit，根据自己的Nvidia驱动支持的最高版本来选择。
conda install cudatoolkit=11.8 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
# 检查一下
conda list cudatoolkit

# 进入 自己环境，安装cudnn，根据前面的cudatoolkit的版本，conda search cudnn搜出可用版本来选择。
conda search cudnn
conda install cudnn=8.9.2.26 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

# 进入 自己建立的环境如python3.8，然后（这一步conda可能要等待1-2个小时）。conda、pip安装二选一即可，推荐pip加 -i 清华源，快点。
# conda install pytorch==2.4.1 torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124 -i https://pypi.tuna.tsinghua.edu.cn/simpleß
pip install torch==2.4.0 torchvision==0.19.0 torchaudio==2.4.0 --index-url https://download.pytorch.org/whl/cu118 -i https://pypi.tuna.tsinghua.edu.cn/simple

pip install pyyaml
# 检查是否有依赖缺失
pip check
pip install pytz idna urllib3
conda install cmake   
...
# 解决完冲突再检查一下
pip check 


conda activate XXX  # 激活conda中的虚拟环境
conda install ipykernel  # 安装丘比特核心。

# 把丘比特核心和conda 建立的环境关联上。
python -m ipykernel install --user --name python3_8 --display-name "Python 3.8 (conda)"
python -m ipykernel install --user --name python3_10 --display-name "Python 3.10 (conda)"

# 多加了或者 虚拟环境已经没有了，删除丘比特和虚拟环境的关联
jupyter kernelspec uninstall python3.7 
# 看关联内核
jupyter kernelspec list 
```





# 九、成功配置案例

​	

## 	案例1

- 系统：Ubuntu 20.04.6 LTS （服务器版本）
- python：Python 3.8.10
- GPU：Tesla V100S-PCIE-32GB
- Nvidia驱动：550.107.02
- 系统Cuda：cuda_12.4
- conda：未安装
- Pytorch：2.4.0+cu118		

​				![image-20240928172228211](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928172228211.png)

![image-20240928172257720](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928172257720.png)

![image-20240928172338819](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928172338819.png)

![image-20240928172404532](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928172404532.png)



## 案例2

- 系统：Ubuntu 20.04.6 LTS （服务器版本）

- python：Python 3.8.19  （conda虚拟环境的python）

- GPU：GRID V100S-16C

- Nvidia驱动：470.82.01 （企业版run安装）

- 系统Cuda：V10.1.243

- conda：miniconda 24.7.1

- Pytorch： 1.10.0	（conda虚拟环境内）	

- cuda：11.3（conda虚拟环境内）

- cudatoolkit: 11.3.1 （conda虚拟环境内）

- cudnn: 8200  （conda虚拟环境内）

  

![image-20240928194018180](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928194018180.png)

![image-20240928194041646](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928194041646.png)

![image-20240928194100718](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928194100718.png)

![image-20240928194133870](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928194133870.png)

![image-20240928194603410](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928194603410.png)

![image-20240929110609679](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240929110609679.png)

![image-20240929110633360](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240929110633360.png)



​		以上只是两个成功安装案例，实际上 安装只要注意 后向支持和小版本支持的匹配原则，是很容易搭配的。



# 附加：可能遇到的问题 



## 1、死进程霸占显存

​		遇到死了很久的进程，却一直霸占着显存

![image-20240927222847362](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240927222847362.png)

kill 这些进程，然后 torch.cuda.empty_cache() 



## 2、出过一次错误

![image-20240928132029174](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928132029174.png)

```sh
# 重启Nvidia服务。
sudo systemctl restart nvidia-persistenced

sudo reboot 
```

![image-20240928132138895](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928132138895.png)



## 3、安装遇到空间不够

![image-20240928133838486](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928133838486.png)

```sh
# 把临时的管道文件夹设置在一个大点的磁盘内，这个文件夹挂着个大磁盘，并且这个是一次性的，下次开机就不生效了。
mkdir -p /data/tmp
export TMPDIR=/data/tmp
```



## 4、Nvidia驱动下载好了但是没有安装上，没有识别到硬件

nvidia-smi 出现这样提示，且/dev下面没有 nvidia相关文件。

`ll /dev | grep nvidia ` 显示空。

![image-20240928191836094](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928191836094.png)

![image-20240928191451148](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928191451148.png)

处理方法：

​		关机，（有虚拟化管理）在虚拟化的管理平台 删除硬件，再 添加硬件；（直接有硬件显卡）拔了，再插上；

![image-20240928191700340](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928191700340.png)

![image-20240928192055059](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928192055059.png)

![image-20240928192116639](GPU%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6%E7%AC%94%E8%AE%B0/image-20240928192116639.png)

如果还没有识别到，见 前面Nvidia驱动安装，巴拉巴拉再来一遍。