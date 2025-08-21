---
title: Linux 安装 MCSManager
date: 2025-08-21 13:49:11
tags: centos, linux, mc, mcsm
---

该文章讲述如何在 Linux 环境下安装并使用 MCSManager 搭建 MC 服务器

## 快速下载

如果你希望你的 MCSManager 可以自动更新, 可以试试官方所给的脚本安装  
因为需要注册到系统服务，一键安装脚本**必须**使用 root 权限运行  

```sh
sudo su -c "wget -qO- https://script.mcsmanager.com/setup_cn.sh | bash"

# 下载完成后使用以下命令启动
systemctl start mcsm-daemon.service
systemctl start mcsm-web.service
```

## 手动下载

切换到安装目录, 你也可以换成其他的目录  

```sh
cd /opt/
```

下载 NodeJS 运行时环境, _如果你已经安装了 NodeJS, 请忽略此步骤_  

```sh
wget https://nodejs.org/dist/v20.11.0/node-v20.11.0-linux-x64.tar.xz
tar -xvf node-v20.11.0-linux-x64.tar.xz
```

添加 NodeJS 到系统环境变量  

```sh
ln -s /opt/node-v20.11.0-linux-x64/bin/node /usr/bin/node
ln -s /opt/node-v20.11.0-linux-x64/bin/npm /usr/bin/npm
```

进入你的安装目录  

```sh
mkdir /opt/mcsmanager/
cd /opt/mcsmanager/
```

下载 MCSManager（如果无法下载可以先科学上网下载再上传到服务器）  

```sh
wget https://github.com/MCSManager/MCSManager/releases/latest/download/mcsmanager_linux_release.tar.gz
```

解压到安装目录  

```sh
tar --strip-components=1 -xzvf mcsmanager_linux_release.tar.gz
```

安装依赖库  

```sh
./install.sh
```

请使用 Screen 程序打开两个终端窗口（或者其他接管程序）  

先启动节点程序  

```sh
./start-daemon.sh
```

在第二个终端启动 Web 面板服务  

```sh
./start-web.sh
```

为网络界面访问 http://localhost:23333/  
一般来说，网络应用会自动扫描并连接到本地守护进程  
默认需要开放的端口：23333 和 24444  

随后只需根据向导进行服务器搭建即可  

## NAT 部署

如果你的云服务器是独立公网IP, 那么以下内容和你无关，如果你的云服务器是 NAT 映射架构（比如[剑客云](https://cloud.swordsman.com.cn/?i0d425e)的 云服务器），请参考本章节  

在搭建完成后, 直接放开 23333 和 24444 端口并不能直接连接  
MCSM 分为管理端（Web）和 守护进程端（Daemon）, 需要分别创建端口映射  

创建管理端端口映射时, 内网端口写 23333 , 公网端口任意  
但创建守护进程端端口映射时, 先看系统给你分配到了哪一个端口, 然后内网端口就也写一样的  

比如这里给我分配的是 7240 端口, 那我就需要内网端口同样填写 7240 端口  

随后来修改 MCSM 的配置，只需要修改守护进程端（Daemon）的即可  

```sh
vi /opt/mcsmanager/daemon/data/Config/global.json
```

修改 `port` 项的值为刚分配的 7240 , 随后按下 Esc 键, 输入 `:wq` 保存并退出编辑器  

重启 MSCM 服务, 随后连接 `http://baka.com:7240`  

根据向导创建管理员账户, 随后来到 `节点` 页面  
系统配置了一个 localhost 节点, 但是状态是离线, 这是因为我们刚刚改掉了守护进程端口并且重启使其生效了, 但是 Web 端并没有修改, 因此 web 端认为守护进程节点寄了, 现在我们把他改正  

点击节点右上角的设置按钮进入设置页面  
将 `远程节点端口` 中默认的 24444 更改为 7240 , 更改其他必填项后点击 `确定` 保存更改  

此时应该节点恢复在线，大功告成! 现在可以去创建服务端了  
