---
layout: post
title: "Ubuntu20.10环境下双显卡的驱动"
date:   2021-03-20
tags: [Linux]
comments: false
toc: true
author: shikamaru
excerpt_separator: ""
---
电脑的配置如下：
```shell
            .-/+oossssoo+/-.               shikamaru@shikamaru-OMEN-Linux 
        `:+ssssssssssssssssss+:`           ------------------------------ 
      -+ssssssssssssssssssyyssss+-         OS: Ubuntu 20.10 x86_64 
    .ossssssssssssssssssdMMMNysssso.       Host: OMEN Laptop 15-en0xxx 
   /ssssssssssshdmmNNmmyNMMMMhssssss/      Kernel: 5.8.0-45-generic 
  +ssssssssshmydMMMMMMMNddddyssssssss+     Uptime: 9 mins 
 /sssssssshNMMMyhhyyyyhmNMMMNhssssssss/    Packages: 2129 (dpkg), 6 (snap) 
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Shell: zsh 5.8 
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   Resolution: 1920x1080 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   DE: GNOME 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   CPU: AMD Ryzen 7 4800H with Radeon G
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   GPU: AMD ATI 07:00.0 Renoir
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   GPU: NVIDIA GeForce GTX 1650 Ti Mobi
 /sssssssshNMMMyhhyyyyhdNMMMNhssssssss/    Memory: 2765MiB / 15428MiB 
  +sssssssssdmydMMMMMMMMddddyssssssss+     
   /ssssssssssshdmNNNNmyNMMMMhssssss/       
    .ossssssssssssssssssdMMMNysssso.        
      -+sssssssssssssssssyyyssss+-          
        `:+ssssssssssssssssss+:`           
            .-/+oossssoo+/-.

```

在安装Ubuntu的时候勾选了默认安装的开源显卡驱动，但是打开发现问题太多，而且很难知道显卡到底有没有起作用，因此决定自己安装显卡驱动。

## 修复重启时报告的 AMD 核显问题

在每次重启或者关机的时候，总是会提示如此下错误：

`***\*ERROR\** VGACON disables amdgpu kernel modesetting**`

粗略地分析之后，可以知道核显的内核某种功能被限制，现在要做的应该就是先解决核显的问题。但是在 AMD 的官网并没有找到相关的 Linux 驱动。在疯狂 Google 之后终于在[这里](https://askubuntu.com/questions/1244376/cant-get-internal-and-external-monitor-working-simultaneously-with-20-04-on-lap)找到了一篇帖子，评论区就有解决方法。而且还顺便有 Nvidia 独显驱动的解决方法。

## AMD 核显驱动支持

1. 编辑 grub 启动目录

```shell
sudo vi /etc/default/grub
```

2. 在 `GRUB_CMDLINE_LINUX_DEFAULT=`一行双引号内的末尾，添加 `amdgpu.exp_hw_support=1`，修改后如下

```shell
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash amdgpu.exp_hw_support=1"
```

3. 保存退出，并更新 grub

```shell
sudo update-grub
```

4. 重启

## Nividia 独显驱动安装

### 卸载随系统安装的开源显卡驱动

1. 将 nouveau driver加入黑名单。在 `/etc/modprobe.d/` 内创建配置文件

	```shell
	sudo vi /etc/modprobe.d/blacklist-nouveau.conf
	```

	在 vim 中编辑文件，加入如下配置：
	
	```shell
	blacklist nouveau
	options nouveau modeset=0
	```
	
2. 保存退出。并更新配置文件，最后重启。
	
	```shell
	sudo update-initramfs -u
	```

### 安装 Nvidia 官方的驱动

1. 安装前检查开源驱动是否已经禁用

   ```shell
   lsmod | grep nouveau
   ```

   若运行后没有输出，则代表禁用成功！

2. 安装 Nvidia 驱动（网上教程很多，我只是其中一种）

   1. 运行`ubuntu-drivers devices`命令，自动列出你的显卡信息，并显示可安装的显卡驱动

   	```shell
   	➜  ~ ubuntu-drivers devices
   	== /sys/devices/pci0000:00/0000:00:01.1/0000:01:00.0 ==
   	modalias : pci:v000010DEd00001F95sv0000103Csd00008788bc03sc00i00
   	vendor   : NVIDIA Corporation
   	model    : TU117M [GeForce GTX 1650 Ti Mobile]
   	driver   : nvidia-driver-450 - distro non-free
   	driver   : nvidia-driver-460 - third-party non-free recommended
   	driver   : nvidia-driver-460-server - distro non-free
   	driver   : nvidia-driver-450-server - distro non-free
   	driver   : xserver-xorg-video-nouveau - distro free builtin
   	```
   	
   2. 可以看到显卡信息`[GeForce GTX 1650 Ti Mobile]`，然后下面列出了可供安装的显卡驱动，其中`nvidia-driver-460`的后面有`recommended`标注，一般按照推荐的来，不然指不定会出什么问题。运行 apt 安装驱动

      ```shell
      sudo apt install nvidia-driver-460
      ```

   3. 重启系统

3. 安装完成后在终端中执行`nvidia-smi`会出现显卡信息

   ```shell
   ➜  ~ nvidia-smi
   Sat Mar 20 21:20:12 2021       
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 460.56       Driver Version: 460.56       CUDA Version: 11.2     |
   |-------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |                               |                      |               MIG M. |
   |===============================+======================+======================|
   |   0  GeForce GTX 165...  Off  | 00000000:01:00.0 Off |                  N/A |
   | N/A   34C    P8     4W /  N/A |    348MiB /  3911MiB |      6%      Default |
   |                               |                      |                  N/A |
   +-------------------------------+----------------------+----------------------+
                                                                                  
   +-----------------------------------------------------------------------------+
   | Processes:                                                                  |
   |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
   |        ID   ID                                                   Usage      |
   |=============================================================================|
   |    0   N/A  N/A      1926      G   /usr/lib/xorg/Xorg                185MiB |
   |    0   N/A  N/A      2446      G   /usr/bin/gnome-shell               49MiB |
   |    0   N/A  N/A      4723      G   ...AAAAAAAAA= --shared-files       38MiB |
   |    0   N/A  N/A      5471      G   ...AAAAAAAAA= --shared-files       26MiB |
   |    0   N/A  N/A      5623      G   ...AAAAAAAA== --shared-files       45MiB |
   +-----------------------------------------------------------------------------+
   ```

## 切换首选显卡

### 编辑 AMD 显卡的配置文件

1. 编辑 amdgpu 的X11配置文件. 文件路径 `/usr/share/X11/sorg.conf.d/`

   ```shell
   sudo vi /usr/share/X11/xorg.conf.d/10-amdgpu.conf
   ```

2. 修改内容如下：

   ```
   Section "OutputClass"
       Identifier "AMDgpu"
       MatchDriver "amdgpu"
       Driver "amdgpu"
       Option "PrimaryGPU" "no"
   EndSection
   ```

### 编辑 Nvidia 的配置文件

1. 修改Nvidia显卡配置

   ```shell
   sudo gedit /usr/share/X11/xorg.conf.d/10-nvidia.conf
   ```

2. 内容如下：

   ```
   Section "OutputClass"
   	Identifier "nvidia"
   	MatchDriver "nvidia-drm"
   	Driver "nvidia"
   	Option "AllowEmptyInitialConfiguration"
   	Option "PrimaryGPU" "yes"
   	ModulePath "/usr/lib/x86_64-linux-gnu/nvidia/xorg"
   EndSection
   ```

### 重启

启动之后，首选显卡就变成了 Nvidia 独显，且通过`nvidia-smi`命令能够查看显卡信息。

