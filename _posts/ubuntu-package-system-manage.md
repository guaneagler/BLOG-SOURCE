---
title: Ubuntu Package System Manage
date: 2017-11-27 21:49:53
categories:
  - Ubuntu
tags:
  - apt
  - apt-get
  - dpkg
description: Introduction of manage ubuntu application, include apt, apt-get, apt-pure, dpkg
---

> 内容剪取自以下 (英文的，描述的比我清楚<..)
>> [apt-get](https://itsfoss.com/apt-get-linux-guide/)
>> [apt](https://itsfoss.com/apt-command-guide/)
>> [difference](https://itsfoss.com/apt-vs-apt-get-difference/)
## 简介
apt, apt-get, apt-cache, dpkg都是debian package system的管理工具，apt-get主要是安装，升级，卸载package的功能；apt-cache主要是搜索package信息的功能；apt则是集成了前面两者的一些常用命令；至于dpkg,可以安装下载到本地的.deb软件，也可以卸载。

### apt & apt-get & apt-cache命令
*这个命令主要作用是用来更新package database的，执行命令后可以查看哪些软件是需要更新的，但是并不执行更新*

```
apt-get update
# 两个命令作用是一样的
apt update
```

*这个命令是对所有软件进行更新的*
```
apt-get upgrade
# 两个命令作用相同
apt upgrade

# 更新所有软件，和上面的不同之处在于，更新的时候会删除已存在的软件
apt-get full-upgrade
apt full-upgrade
```
*安装新软件*
```
apt-get install <package name>
# 两命令作用相同
apt install <package name>
# 安装多个软件，空格分割
apt install <package name1> <package name2>
```
*删除软件*
```
apt-get remove <package name>
# 两命令作用相同
apt remove <package name>
# 也是删除软件，不同之处在于删除软件的同时，还会删除配置文件
apt-get purge <package name>
apt purge <package name>
```
*清理空间*
```
# 清理retrieved package files（下载到本机的包文件）
apt-get clean
# 清理存在更新的包文件
apt-get autoclean
# 清理已经被删除的软件的依赖
apt-get autoremove
# apt清理只有这个
apt autoremove
```
*查找软件*
```
apt-cache search <search tags>
命令作用相同
apt search <search tags>
# 查看详情
apt show <package name>
```
*apt & apt-get*
两者不同之处，在这里,我觉得好像就是说apt是更加推荐使用的工具，另外就是apt属于apt-get的上一级，好像命名和概念上更容易接受一些。

### dpkg
*安装.deb软件*
```
sudo dpkg -i *.deb
```
*卸载.deb软件*
```
sudo dpkg -r 软件名字
```

## 软件源介绍
> 详见[Ubuntu软件源详解](https://woshijpf.github.io/%E5%88%A9%E5%99%A8/2016/10/23/Ubuntu%E8%BD%AF%E4%BB%B6%E6%BA%90%E8%AF%A6%E8%A7%A3.html)

### 软件源分类
1.Ubuntu官方源
2.PPA软件源
我们日常用的（包括系统默认的和阿里源等替换源）都是官方软件源，默认软件源是ubuntu软件源，我们经常用的替换源是为了增加访问下载速度，毕竟有一道墙。。。，这些替换源会定期的将ubuntu源同步过来，他们都属于官方源。
>以前我一直不明白既然有了官方的软件源，为什么还多出了一个 PPA 软件源，统一用 Ubuntu 官方源不是挺好吗？ 直到在网上看了有关有关 PPA 的介绍后，我才了解了它的真正用途。
PPA 源出现的背景是因为系统自带的源是很有限的，我们肯定需要一些其他的软件包然而如果是直接下载deb格式的文件的话，又不能获取到更新和维护，所以这就用到了十分重要的 PPA 源了。
所谓 PPA 源，就是指 “Personal Package Archives” ，也就是个人软件包集。这其实是一个网站，即－launchpad.net。Launchpad 是 Ubuntu 母公司 Canonical 有限公司所架设的网站，是一个提供维护、支援或联络 Ubuntu 开发者的平台。由于不是所有的软件都能进入 Ubuntu 的官方的软件库，launchpad.net 提供了 PPA，允许开发者建立自己的软件仓库，自由的上传软件。供用户安装和查看更新。

### 软件源替换
可以到[这里](https://segmentfault.com/a/1190000000375848)找到软件源，替换掉/etc/apt/sources.list文件的内容即可。
PPA软件源
添加 PPA 软件源的命令：sudo add-apt-repository ppa:user/ppa-name
删除 PPA 软件源的命令：sudo add-apt-repository –remove ppa:user/ppa-name
PPA软件源的source list存在于 /etp/apt/sources.list.d文件夹下面，里面文件内容与官方源是相同格式的。

## 小结
装好系统，先更新软件源，然后依次执行sudo apt update，sudo apt upgrade
安装软件，先网上找软件，看能不能用sudo apt install命令安装，可以的话，执行安装
没有的话可以找一下是否有deb包可以下载，下载后使用dpkg -i 安装它
Ubuntu14默认的官方源安装lamp，php是5.5的，如果要安装5.6或者7的php，就需要添加ppa源，添加之后执行update，再进行安装





