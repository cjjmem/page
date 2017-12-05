---
layout: post
title:  "chromium从编译到入门开发"
date:   2017-12-05 10:56:00 +0800
categories: jekyll update
---

# chromium从编译到入门开发

chromium因为环境问题编译一直出现报错，断断续续的等待终于编译出第一个chromium浏览器,然而这才是开始，接下来让我们认识一下chromium....

*以下资料来源于网络以及开发过程中整理而得*。

## chromium 简介

## 目录结构

* **cc 层** chromium 合成层，实现网页绘制并贴到屏幕上等操作
* **base库** chromium基础库，类似字符串操作等一系列c++基础库
* **third_party/WebKit/public/platform/Platform.h** ，各种和操作系统打交道的接口，例如菜单、文件读写、窗口操作等
* **v8** js引擎
* **skia** 为Android开发谷歌的Skia的图形库
* **android_webview** 提供适合集成到Android平台的src /content的封装接口。
* **apps** chrome应用相关。
* **base** 所有子项目之间共享的通用代码。它包含像字符串操作，通用程序等。只有当超过一个顶级项目之间共享的代码才能放到这里。
* **breakpad** 谷歌的开源崩溃报告的项目。直接从谷歌代码版本库拉取。
* **build** 构建相关的配置，由所有项目共享。
* **chrome** chromium浏览器主程序模块实现代码，核心代码。
* **chrome/test/data** 运行某些测试的数据文件。
* **components** 浏览器依赖的组件目录。
* **content** 一个多进程沙箱浏览器渲染页面的核心代码。
* **net** 为chromium开发的网络功能库。
* **sandbox** 沙箱项目，阻止渲染器修改系统。
* **sql** sqlite的封装。
* **testing** 用来单元测试的代码。
* **third_party** 第三方库，如图像解码器和压缩库等。
* **ui/GFX** 共享
* **图形类** 这些是Chromium的图形用户界面的基础。
* **ui/view** UI开发的简单框架，提供渲染，布局和事件处理一个简单的框架。大部分的浏览器的用户界面的在该系统中实现。该目录包含了基本对象。

* **url** 谷歌的开源URL解析和标准化库。


## 编辑器使用

入门开发者觉得郁闷就是看不到网上所有生成多个.sln解决方案，而使用
由于打开all.sln解决方案里面涉及5000多个项目，费时。建议使用，Sublime Text 编译器

* 安装g++
* 安装Ctags


## 代码修改积累

### 1.配置中文默认搜索引擎

在src\chrome\browser\search_engines\template_url_prepopulate_data.cc中修改

在engines_CN中加入“chinaso“，就是prepopulated_engines.json定义的搜索引擎

```
const PrepopulatedEngine* engines_CN[] =
    { &chinaso, &baidu, &sogou, &soso, &google };
```

[参考](http://blog.csdn.net/ljm198745/article/details/42098985)

### 2.修改浏览器名字,LOGO,文案等

* 浏览器名称
```
..\src\chrome\app\theme\chromium\BRANDING

COMPANY_FULLNAME=The Chromium Authors
COMPANY_SHORTNAME=The Chromium Authors
PRODUCT_FULLNAME=Chromium
PRODUCT_SHORTNAME=Chromium
PRODUCT_INSTALLER_FULLNAME=Chromium Installer
PRODUCT_INSTALLER_SHORTNAME=Chromium Installer
COPYRIGHT=Copyright 2017 The Chromium Authors. All rights reserved.
MAC_BUNDLE_ID=org.chromium.Chromium
MAC_CREATOR_CODE=Cr24


```

* LOGO图标

```
..\src\chrome\app\theme\chromium
..\src\chrome\app\theme\chromium\win\chromium.ico
..\src\chrome\app\theme\chromium\win\tiles\Logo.png
..\src\chrome\app\theme\chromium\win\tiles\SmallLogo.png
..\src\chrome\app\theme\default_100_percent
..\src\chrome\app\theme\default_200_percent
```

* 所有各种语言的文案 **.xtb**语言包
```
..\src\chrome\app\resources
..\src\components\strings
```

## 增加默认插件

由于对Chromium源码不熟悉，看到网上各种关于添加默认插件Blog比较旧，采用以下方法进行尝试：

```c++
// ..src\chromiums\chromium\src\chrome\browser\extensions\component_loader.cc

// 注释默认安装

void ComponentLoader::AddHotwordAudioVerificationApp() {
// #if defined(ENABLE_HOTWORDING)
//  if (HotwordServiceFactory::IsAlwaysOnAvailable()) {
    Add(IDR_HOTWORD_AUDIO_VERIFICATION_MANIFEST,
        base::FilePath(FILE_PATH_LITERAL("hotword_audio_verification")));
//  }
// #endif
}

```



## 参考文档

* [关于硬件加速的基本知识](http://dev.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome)
* [chromium定制启动首页](http://blog.csdn.net/jianglong0156/article/details/45646271)
* [文档](https://chromium.googlesource.com/chromium/src/+/master/docs/)
* [](https://ahangchen.gitbooks.io/chromium_doc_zh/content/en/General_Architecture/)