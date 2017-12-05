---
layout: post
title:  "windows10环境下构建chromium开源浏览器"
date:   2017-12-05 09:56:00 +0800
categories: jekyll update
---


# 构建chromium

## 编译环境
* 操作系统 win10 企业版 1709
* 编辑工具 Visual Studio 2017 社区免费版
* 内存16G
* 磁盘大小250G

## 安装

* 编辑工具 Visual Studio 2017
  * [下载地址](https://www.visualstudio.com/zh-hans/thank-you-downloading-visual-studio/?sku=Community&rel=15)
* WINDOWS SDK 10
  * [下载地址](https://developer.microsoft.com/zh-cn/windows/downloads/windows-10-sdk)
* Windows Driver Kit for Windows 10  驱动程序工具包 (1709)
  * [下载地址](https://developer.microsoft.com/zh-cn/windows/hardware/windows-driver-kit)
* depot_tools 工具
  [下载地址](https://storage.googleapis.com/chrome-infra/depot_tools.zip)


## 设置系统环境变量


系统变量 | 值 | 备注
---|---|---
PATH | C:\src\depot_tools | depot_tools安装目录 移动到最顶端
DEPOT_TOOLS_WIN_TOOLCHAIN | 0 | 告诉depot_tools使用您本地安装的Visual Studio版本（缺省情况下，depot_tools将尝试使用Google内部版本）
GYP_GENERATORS | msvs-ninja,ninja | 指定编译工具
GYP_MSVS_OVERRIDE_PATH | E:\Program Files (x86)\MV2017 | Visual Studio 安装目录
GYP_DEFINES | Chromium buildtype=Dev component=shared_library disable_nacl=1 | 依赖


## 部署过程

公司提供专用的VPN通道，无需解决翻墙问题。

### 一.安装Visual Studio 2017 工具

* 使用C++的桌面开发
* Visalt Studio 扩展开发
* [单个组件]Windows 10 SDK (10.0.15063.0) for Desktop c++ [x86 and x64]
* [单个组建] MFC 和
* Windows DriverKit(通过链接下载安装)[下载地址](https://developer.microsoft.com/zh-cn/windows/hardware/windows-driver-kit)

### 二.通过depot_tools工具获取代码

1. 执行gclient命令，安装python + msysgit
```
gclient

// 执行成功提示版本信息
python -v
git
```
2. 设置git配置信息

```
git config --global user.name "My Name"
git config --global user.email "my-name@chromium.org"
git config --global core.autocrlf false
git config --global core.filemode false
git config --global branch.autosetuprebase always
```
3. 创建chromium为目录

```
mkdir chromium && cd chromium
```

4. 运行该fetch工具depot_tools检查代码及其依赖关系，下载代码（大约18G左右）
```
// 建议不要使用 fetch --no-history chromium 命令
// 获取全部代码包括git记录
fetch chromium
```

### 三.根据需要切换分支tag版本

1. 获取tags

```
git fetch -t
git checkout -b <local-branch-name> tags/<tag name>
```

示例：

```
https://chromium.googlesource.com/chromium/src.git/+refs
git checkout -b <local-branch-name> tag的sha1值
```

2. 同步子projects,根据src\DEPS内的配置将依赖的库checkout到需要的revision [Uri](https://chromium.googlesource.com/chromium/src.git/+log/57.0.2987.133/DEPS)

```
gclient sync
```

在编译时遇到fatal: reference is not a tree: webrtc

```
// 用这个版本替换了DEPS文件中的版本
git checkout d68fcc42256f0f6483d562aa69531091560ff9f2
gclient sync
```

### 四.使用Gn构建工具生成编译方案

1. 修改编译方案

```
::启用official build
::修改src\build\config\BUILDCONFIG.gn，将is_official_build的值改为true

::去掉显示头文件的包含树
::修改src\build\toolchain\win\BUILD.gn，将/showIncludes删除掉
```

2. 生成ninja编译脚本 [gn配置](
https://sites.google.com/a/chromium.org/dev/developers/gn-build-configuration)
```
cd src
gn gen out/Default
```

3. 修改ninja编译参数，如target_os="win"，target_cpu="x64"等  [参考链接](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs/quick_start.md)

```
gn args out/Default


is_debug = false
symbol_level = 0
enable_nacl = false
target_cpu = "x64"
target_os = "win"
google_api_key = "AIzaSyBPsfyKutEqu_7liVtbYqrY7QKlYsd8xoU"
google_default_client_id = "1000406808719-ghvvdlf749dvc6cvvqpb9uqi9l66tg9d.apps.googleusercontent.com"
google_default_client_secret = "XWhKLxZGu1Q70PSB5ikSycTb"

```

### 五.开始编译

```
ninja -C out/Default chrome
```

经过10小时左右的编译，终于成功编译出第一个chromium浏览器


## 参考文档
[Checking out and Building Chromium for Windows](https://chromium.googlesource.com/chromium/src/+/master/docs/windows_build_instructions.md)
[docs](http://blog.csdn.net/baihacker/article/details/68948978?locationNum=9&fps=1)
[代码搜索](https://cs.chromium.org/chromium/src/chrome/browser/extensions/api/preference/preference_api.cc?type=cs&sq=package:chromium)