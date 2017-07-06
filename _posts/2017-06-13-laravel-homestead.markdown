---
layout: post
title:  "windows下搭建Laravel Homestead"
date:   2017-06-13 20:29:03 +0800
categories: jekyll update
---

# Laravel Homestead

Laravel Homestead是一个官方的、预封装的Vagrant“箱子”，Homestead能运行在所有的Windows、Mac或Linux系统上，它包含了Nginx、PHP 5.6、MySQL、Postgres、Redis、Memcached和你开发神奇的Laravel应用程序需要的所有其它软件。让初学者不用为环境问题而烦恼，減少浪费时间在架设 Laravel。
由于网络、操作系统等不同安装过程可能出现各种各样的问题，这里主要不是介绍如何解决这些问题。本经验通过Windows 7下成功的安装过程向大家介绍如何安装Homestead2.0。

**安裝软件:**

- **VirtualBox**  Oracle 公司的虚拟机软件, 能运行在当前大部分流行的系统上 下载地址 https://www.virtualbox.org/wiki/Downloads
- **Vagrant** 供一种命令行接口, 允许自动化安装虚拟机, 并且因为是脚本编写成的 provision 文本文件, 给共享虚拟机配置提供了可能, Homestead 正是构建在 Vagrant 之上; 下载地址：https://www.vagrantup.com/
- **Laravel Homestead** 是官方预封装的一个 Vagrant Box，它是一台虚拟机的原型, 用来快速生产一个配置完整的服务器虚拟机, 运行在 VirtualBox 上.

# vagrant box add laravel/homestead

如果网络正常就慢慢等待下载然后添加，这里由于网络的原因我试了很多次都失败了，最后直接复制下载链接
 https://atlas.hashicorp.com/laravel/boxes/homestead/versions/2.1.0/providers/virtualbox.box下载保存到F:\virtualbox.box，然后通过

vagrant box add laravel/homestead F:\virtualbox.box


