---
layout: post
title:  "如何在windows下搭建git服务器"
date:   2017-06-11 18:00:03 +0800
categories: jekyll update
---

# gitblit

Gitblit 是一个纯 Java 库用来管理、查看和处理 Git 资料库。相当于 Git 的 Java 管理工具。

**依赖环境:**

- **java** 下载地址 http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html
- **gitblit** 下载地址：http://dl.bintray.com/gitblit/releases/gitblit-1.6.2.zip(以windows版为例)，并将gitblit-1.6.2.zip解压


## 修改配置参数

以${baseFolder}代表gitblit根目录，在${baseFolder}\data目录下有一个名为gitblit.properties的配置文件，用于配置gitblit

```
git.repositoriesFolder = ${baseFolder}/git      配置仓库目录，用于放置创建的仓库，默认路径为${baseFolder}/git
git.acceptedPushTransports = HTTP HTTPS SSH     可使用的传输协议，默认支持HTTP,HTTPS,SSH，还有另外一种为GIT
web.siteName = gitblit                          指定站点名称，此名称会作为网站页面标题显示
server.httpPort = 8080                            HTTP协议端口，默认为0，表示禁用此协议，为了安全性起见可禁用此协议
server.httpsPort = 8443                         HTTPS协议端口，默认为8443，如果0，表示禁用此协议
server.certificateAlias = localhost             证书别名，该别名是一主机名，使用该别名后只能通过该主机名进行访问Web页面
server.storePassword = gitblit                  服务端KeyStore密码，该密码在生成服务器证书时需要使用
```


## 启动gitblit

执行${baseFolder}\gitblit.cmd脚本。

## 创建用户

服务器启动后，如果你没有禁用http协议，在浏览器中访问http://127.0.0.1::8080/就能看到gitblit界面了。
gitblit中有个管理员账号，用户名与密码都为admin，登录后进入用户管理界面添加一个用户，比如为用户名与密码都为：cjjmem，邮箱为：cjjmem@163.com。

## 仓库迁移
如果你想仓库迁移到其他服务器，直接拷贝git.repositoriesFolder的配置仓库目录到其他服务器。

## 参考资料
- [博客](http://blog.csdn.net/xtayfjpk/article/details/45669335)
- [gitlab](https://about.gitlab.com/gitlab-com/)
