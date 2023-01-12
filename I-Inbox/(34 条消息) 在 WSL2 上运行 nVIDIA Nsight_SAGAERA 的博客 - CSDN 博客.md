---
url: https://blog.csdn.net/cyr20040123/article/details/122532275
title: (34 条消息) 在 WSL2 上运行 nVIDIA Nsight_SAGAERA 的博客 - CSDN 博客
date: 2023-01-12 11:30:55
tag: 
summary: 
---
## 第一步：打开 [WSL](https://so.csdn.net/so/search?q=WSL&spm=1001.2101.3001.7020) 的 ssh 连接

ref: [ssh 连接 win ubuntu_使用 SSH 终端连接 WSL_倔强的猫的博客 - CSDN 博客](https://blog.csdn.net/weixin_28676983/article/details/112175722 "ssh连接win ubuntu_使用SSH终端连接WSL_倔强的猫的博客-CSDN博客")

### **1. 在 WSL 中运行 ifconfig 获得 IP**

![](https://img-blog.csdnimg.cn/img_convert/510d7265634469bc3e5eb110621c51e6.png)

### **2. 安装 SSH 服务**

sudo apt install [openssh](https://so.csdn.net/so/search?q=openssh&spm=1001.2101.3001.7020)-server

### **3. 打开 22 端口和权限**

sudo vim /etc/ssh/sshd_config，修改两项：

![](https://img-blog.csdnimg.cn/img_convert/7514f5323880a1fcb58973a013bb520f.png)

(1) 把 Port22 和进阶的几行注释去掉

(2) PasswordAuthentication no 改为 PasswordAuthentication yes

### **4. 重启 SSH**

sudo service ssh restart

## 第二步：安装启动 Nsight

### 1. 下载安装 Nsight

在 [NVIDIA Nsight Systems | NVIDIA Developer](https://developer.nvidia.com/zh-cn/nsight-systems " NVIDIA Nsight Systems | NVIDIA Developer ") 这里注册下载安装 Nsight。同时确保本机已经安装 CUDA 和显卡驱动。

### 2. 运行 Nsight 添加 [SSH 连接](https://so.csdn.net/so/search?q=SSH%E8%BF%9E%E6%8E%A5&spm=1001.2101.3001.7020)

![](https://img-blog.csdnimg.cn/35365b5fc5d443f3acd239462bae514d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU0FHQUVSQQ==,size_20,color_FFFFFF,t_70,g_se,x_16)

 通过 Configure targets，添加 WSL 的 IP，并选择是否需要密码连接

![](https://img-blog.csdnimg.cn/5d7439a565574c62902a6555c2ae8ac5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU0FHQUVSQQ==,size_20,color_FFFFFF,t_70,g_se,x_16)

## 完成