---
layout: post
title: "Linux下JDK和Eclipse安装教程"
date:   2019-09-03
tags: [Linux]
comments: false
toc: true
author: shikamaru
---
//全文涉及到路径的地方均为usr内，文件操作需要管理员权限。  
//如果需要安装到其他路径，自行进行修改  
//作者：陈斯特洛夫斯基  
<!-- more -->
## 第一步：下载所需版本的JDK和Eclipse安装包

> https://www.eclipse.org/downloads/packages/    //eclipse下载地址  
>   选择Eclipse IDE for Enterprise Java Developers->Linux 64-bit  
> https://www.oracle.com/technetwork/java/javase/downloads/index.html  //JDK下载地址  
>   我选择的是Java SE 12.0.2  

## 第二步：将两个tar.gz压缩包解压至选定路径，在usr路径下（需管理员权限）

> 解压步骤自行百度（Ubuntu和Deeping均有内置的解压缩程序,方便得一批）

## 第三步：配置JDK环境变量
   打开终端  
   为方便键入，先将解压过的JDK12.0.2文件夹重命名为jdk  
   终端内打开解压后文件夹所在的路径（以usr为例）

```
cd /usr
sudo mv jdk12.0.2/ jdk/
```

   返回主路径

```
cd
```

   打开环境变量配置文件

```
sudo vim /etc/profile
```

   按a开始编辑  
   将以下内容添加到文件末尾

```
export JAVA_HOME=/usr/jdk
export JAVA_BIN=/usr/jdk/bin
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME JAVA_BIN PATH CLASSPATH
```

   退出编辑保存

```
:wq
```

## 第四步：将环境变量链接至eclipse路径内
   终端进入eclipse目录，执行以下命令

```
mkdir jre
cd jre
ln -s /usr/jkd/bin bin
```

## 第五步：运行eclipse吧！享受（简直放屁