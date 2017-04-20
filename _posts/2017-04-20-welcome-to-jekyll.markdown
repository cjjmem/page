---
layout: post
title:  "如何使用Jekyll搭建博客!"
date:   2017-04-20 18:10:03 +0800
categories: jekyll update
---

# 环境要求

[ruby](https://www.ruby-lang.org/en/documentation/installation/)

*安装成功后，可以设置淘宝源，以防国内访问/安装失败。*

{% highlight shell %}
gem sources -a http://gems.ruby-china.org/
{% endhighlight %}

注：
在更改源的时候会出现以下报错
{% highlight shell %}
SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed
{% endhighlight %}

*解决办法*
1. 在http://curl.haxx.se/ca/cacert.pem下载证书，放在ruby安装目录下

2. 设置环境变量SSL_CERT_FILE，指向证书文件

# 搭建

#### 安装jekyll 3.0的版本

{% highlight shell %}
gem install jekyll -v '3.0'
{% endhighlight %}

#### 安装minima、bundle

{% highlight shell %}
gem install minima
gem install bundle
{% endhighlight %}

所需工具已经准备好了，现在可以开始搭建了

#### 创建项目
{% highlight shell %}
jekyll new blog
{% endhighlight %}

#### 本地serve服务
{% highlight shell %}
jekyll serve
{% endhighlight %}

#### 项目构建
{% highlight shell %}
jekyll build
{% endhighlight %}

#### 其他命令
{% highlight shell %}
  docs
  import
  build, b              Build your site
  clean                 Clean the site (removes site output and metadata file) without building.
  doctor, hyde          Search site and print specific deprecation warnings
  help                  Show the help message, optionally for a given subcommand.
  new                   Creates a new Jekyll site scaffold in PATH
  new-theme             Creates a new Jekyll theme scaffold
  serve, server, s      Serve your site locally
{% endhighlight %}


# 参考资料

[ruby](https://www.ruby-lang.org/en/documentation/installation/) https://www.ruby-lang.org/en/documentation/installation/

[jekyll-docs](https://jekyllrb.com/docs/home) https://jekyllrb.com/docs/home

[jekyll-gh](https://github.com/jekyll/jekyll) https://github.com/jekyll/jekyll

[jekyll-talk](https://talk.jekyllrb.com/) https://talk.jekyllrb.com/
