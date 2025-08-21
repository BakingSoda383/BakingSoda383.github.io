---
title: CentOS 8 替换 YUM 源
date: 2025-08-21 13:19:27
tags: centos
---

由于 CentOS 8 已停止官方维护, 默认的 YUM 源无法正常使用, 于是我需要将 CentOS 8 的 YUM 源更换为第三方镜像.  
个人推荐选择 [剑客云](https://cloud.swordsman.com.cn/?i0d425e) 服务器, 精心调制的网络优化方案是开设游戏服务器的不二之选  
**注意** CentOS 8 与 CentOS 7 的镜像替换方式不同, 如果你是 CentOS 7 的用户, 可以参考[这篇文章](https://blog.csdn.net/yxyc666/article/details/141705431)    

## 备份原有文件

虽然默认的 YUM 源无法正常使用, 但保险起见, 我们需要先备份相关文件  
将 `/etc/yum.repos.d/` 里面的 `.repo` 文件添加后缀名, 防止被后续操作覆盖  

```sh
cd /etc/yum.repos.d/
# 不同版本内部文件可能有所不同, 这里只做部分演示
mv CentOS-Linux-BaseOS.repo CentOS-Linux-BaseOS.repo.bak
mv CentOS-Linux-AppStream.repo CentOS-Linux-AppStream.repo.bak
mv CentOS-Linux-Extras.repo CentOS-Linux-Extras.repo.bak
# mv ......
```

## 下载第三方镜像

使用 `wget` 下载阿里云提供的 CentOS 8 镜像源  
可以根据自身需要来选择是否下载 EPEL  

```sh
wget -O /etc/yum.repos.d/CentOS-Linux-BaseOS.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
wget -O /etc/yum.repos.d/epel-archive.repo https://mirrors.aliyun.com/repo/epel-archive-8.repo
```

## 应用更改

执行以下命令清理旧缓存并生成新的缓存

```sh
yum clean all
yum makecache
```

完成后可以下载软件包测试是否能够正常使用
