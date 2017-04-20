---
layout: post
title:  "Composer的安装与使用"
date:   2017-04-20 18:11:03 +0800
categories: jekyll update
---

# Composer

# 简介
PHP包管理

# 安装

windows 下安装 [composer](https://getcomposer.org/download/) 下载Composer-Setup.exe

Linux 下安装

局部安装 使用命令(php composer.phar)
```
// 下载链接
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
// 检验
php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
// 执行下载包
php composer-setup.php
// 删除包
php -r "unlink('composer-setup.php');"
```

全局安装
```
// 下载composer.phar包
curl -sS https://getcomposer.org/installer | php
// 移动文件到全局
mv composer.phar /usr/local/bin/composer
```


# 使用

## 引入第三方库

## 自动加载

## 包开发
