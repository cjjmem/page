---
layout: post
title:  "Browsersync实时代码监控工具"
date:   2017-05-19 18:09:03 +0800
categories: jekyll update
---

# BrowserSync

Browsersync能让浏览器实时、快速响应您的文件更改（html、js、css、sass、less等）并自动刷新页面


## 安装

### nodejs
下载[nodejs download](http://nodejs.org/download/)

### BrowserSync

全局安装
```ssh
npm install -g browser-sync
```

项目安装
```ssh
npm install --save-dev browser-sync
```
## 启动

监控所有js\css\html文件
```ssh
browser-sync start --server --files "**/*.css, **/*.html"
```
启动后直接访问 http://localhost:3000

### package.json文件
有时候为了方便启动BrowserSync进行代码监控可以把命令写在package.json文件中
```js
{
  "name": "carousel-js-css",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "sync":"browser-sync start --server --files **/*.css, **/*.html",
    "syncs":"browser-sync start --server --files css/*.css, *.html",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```
### 轮播项目实战

#### 目录结构

```js
- assets
    - js
        index.js
    - css
        - demo.css
- index.html
- package.json
```

* 配置文件

package.json

```js
{
  "name": "carousel-js-css",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "sync":"browser-sync start --server --files **/*.css, **/*.html",
    "syncs":"browser-sync start --server --files 'css/*.css, *.html'",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "cjjmem",
  "license": "ISC",
  "devDependencies": {
    "browser-sync": "^2.18.11"
  }
}

```



* 静态文件

index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>轮播示列</title>
    <link rel="stylesheet" href="assets/css/demo.css">
</head>
<body>
<h2>Js实现</h2>
<div class="wrapper-js">
    <div class="container-js marquee">
        <p>新浪新闻</p>
        <p>百度新闻</p>
        <p>网易新闻</p>
        <p>搜狗新闻</p><!-- 辅助元素，为实现循环轮播 -->
    </div>
</div>
<h2>CSS实现</h2>
<div class="wrapper-css">
    <div class="container-css marquee">
        <p>新浪新闻</p>
        <p>百度新闻</p>
        <p>网易新闻</p>
        <p>搜狗新闻</p><!-- 辅助元素，为实现循环轮播 -->
    </div>
</div>
</body>
<script src="assets/js/index.js"></script>
</html>
```

* js文件

assets/js/index.js

```js
/**
 * [marquee 轮播效果]
 * @param  {[type]} ele      [轮播元素容器]
 * @param  {[type]} delay    [轮播间隔时间]
 * @param  {[type]} duration [轮播动画时间]
 * @return {[type]}          [description]
 */
function marquee(ele, delay, duration) {
    if (!ele) {
        return false;
    }

    delay = delay || 2000;
    duration = duration || 500;
    var height = ele.clientHeight;
    var active = true;
    var len = ele.querySelectorAll('p').length - 1;

    setTimeout(function() {
        active = true;
        ele.scrollTop = 1;
        setInterval(function() {
            if (!active) return;

            if (ele.scrollTop % height === 0) {
                active = false;
                setTimeout(function() {
                    active = true;
                    ele.scrollTop += 1;
                }, delay);
                if (ele.scrollTop == len * height) {
                    ele.scrollTop = 0;
                }
            } else {
                ele.scrollTop += 1;
            }
        }, duration / height);
    }, delay);
}

marquee(document.querySelector('.marquee'));
```
* 样式文件

assets/css/demo.css

```css
.marquee p{
    z-index: 99999;
    display: block;
    width: 100%;
    padding: 0px;
    text-align: center;
    color: #333;
    background: #77e6e5;
    line-height: 24px;
    font-size: 14px;
}


.wrapper-js {
    width: 200px;
    height: 30px;
    margin: 10px;
}
.container-js {
    height: 30px;
    overflow: hidden;
}
.container-js p {
    width: 100%;
    height: 30px;
    margin: 0;
    line-height: 30px;
    font-size: 18px;
}

@-webkit-keyframes marquee {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
    }
    27% {

        -webkit-transform: translate3d(0, 0, 0);
    }
    33% {
        -webkit-transform: translate3d(0, -100%, 0);
    }
    60% {
        -webkit-transform: translate3d(0, -100%, 0);
    }
    67% {
        -webkit-transform: translate3d(0, -200%, 0);
    }
    94% {
        -webkit-transform: translate3d(0, -200%, 0);
    }
    100% {
        -webkit-transform: translate3d(0, -300%, 0);
    }
}
@keyframes marquee {
    0% {
        transform: translate3d(0, 0, 0);
    }
    /* 100/3 * (2s/2.5s) => 26.7% => 27% */
    27% {
        transform: translate3d(0, 0, 0);
    }
    /* 100/3 =>33.3 => 33% */
    33% {
        transform: translate3d(0, -100%, 0);
    }
    60% {
        transform: translate3d(0, -100%, 0);
    }
    67% {
        transform: translate3d(0, -200%, 0);
    }
    94% {
        transform: translate3d(0, -200%, 0);
    }
    100% {
        transform: translate3d(0, -300%, 0);
    }
}
.wrapper-css {
    width: 200px;
    height: 30px;
    margin: 10px;
    overflow: hidden;
}
.container-css {
    height: 30px;
    -webkit-animation: marquee 7.5s linear infinite;/* 2.5s(2s + 0.5s) * 3 => 7.5s */
    animation: marquee 7.5s linear infinite;
}
.container-css p {
    width: 100%;
    height: 30px;
    margin: 0;
    line-height: 30px;
    font-size: 18px;
}
```




<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>轮播示列</title>
    <link rel="stylesheet" href="assets/css/demo.css">
    <style>
    .marquee p{
        z-index: 99999;
        display: block;
        width: 100%;
        padding: 0px;
        text-align: center;
        color: #333;
        background: #77e6e5;
        line-height: 24px;
        font-size: 14px;
    }

    .marqueejs p{
        background: #ea3b3b;
        color: #fff;
    }


    .wrapper-js {
        width: 200px;
        height: 30px;
        margin: 10px;
    }
    .container-js {
        height: 30px;
        overflow: hidden;
    }
    .container-js p {
        width: 100%;
        height: 30px;
        margin: 0;
        line-height: 30px;
        font-size: 18px;
    }

    @-webkit-keyframes marquee {
        0% {
            -webkit-transform: translate3d(0, 0, 0);
        }
        27% {

            -webkit-transform: translate3d(0, 0, 0);
        }
        33% {
            -webkit-transform: translate3d(0, -100%, 0);
        }
        60% {
            -webkit-transform: translate3d(0, -100%, 0);
        }
        67% {
            -webkit-transform: translate3d(0, -200%, 0);
        }
        94% {
            -webkit-transform: translate3d(0, -200%, 0);
        }
        100% {
            -webkit-transform: translate3d(0, -300%, 0);
        }
    }
    @keyframes marquee {
        0% {
            transform: translate3d(0, 0, 0);
        }
        /* 100/3 * (2s/2.5s) => 26.7% => 27% */
        27% {
            transform: translate3d(0, 0, 0);
        }
        /* 100/3 =>33.3 => 33% */
        33% {
            transform: translate3d(0, -100%, 0);
        }
        60% {
            transform: translate3d(0, -100%, 0);
        }
        67% {
            transform: translate3d(0, -200%, 0);
        }
        94% {
            transform: translate3d(0, -200%, 0);
        }
        100% {
            transform: translate3d(0, -300%, 0);
        }
    }
    .wrapper-css {
        width: 200px;
        height: 30px;
        margin: 10px;
        overflow: hidden;
    }
    .container-css {
        height: 30px;
        -webkit-animation: marquee 7.5s linear infinite;/* 2.5s(2s + 0.5s) * 3 => 7.5s */
        animation: marquee 7.5s linear infinite;
    }
    .container-css p {
        width: 100%;
        height: 30px;
        margin: 0;
        line-height: 30px;
        font-size: 18px;
    }
    </style>
</head>
<body>
<h2>Js实现</h2>
<div class="wrapper-js">
    <div class="container-js marquee marqueejs">
        <p>新浪新闻</p>
        <p>百度新闻</p>
        <p>网易新闻</p>
        <p>搜狗新闻</p><!-- 辅助元素，为实现循环轮播 -->
    </div>
</div>
<h2>CSS实现</h2>
<div class="wrapper-css">
    <div class="container-css marquee">
        <p>新浪新闻</p>
        <p>百度新闻</p>
        <p>网易新闻</p>
        <p>搜狗新闻</p><!-- 辅助元素，为实现循环轮播 -->
    </div>
</div>
</body>
<script>
/**
 * [marquee 轮播效果]
 * @param  {[type]} ele      [轮播元素容器]
 * @param  {[type]} delay    [轮播间隔时间]
 * @param  {[type]} duration [轮播动画时间]
 * @return {[type]}          [description]
 */
function marquee(ele, delay, duration) {
    if (!ele) {
        return false;
    }

    delay = delay || 2000;
    duration = duration || 500;
    var height = ele.clientHeight;
    var active = true;
    var len = ele.querySelectorAll('p').length - 1;

    setTimeout(function() {
        active = true;
        ele.scrollTop = 1;
        setInterval(function() {
            if (!active) return;

            if (ele.scrollTop % height === 0) {
                active = false;
                setTimeout(function() {
                    active = true;
                    ele.scrollTop += 1;
                }, delay);
                if (ele.scrollTop == len * height) {
                    ele.scrollTop = 0;
                }
            } else {
                ele.scrollTop += 1;
            }
        }, duration / height);
    }, delay);
}

marquee(document.querySelector('.marquee'));</script>
</html>



* [BrowserSync官网](http://www.browsersync.cn)
* [BrowserSync API说明文档](http://www.browsersync.cn/docs/api/)
* [轮播项目实战]()