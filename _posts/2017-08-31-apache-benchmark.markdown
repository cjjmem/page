---
layout: post
title:  "小小压力测试"
date:   2017-08-31 19:56:00 +0800
categories: jekyll update
---

# Apache benchmark
简称ab ，是非常有名又小巧的压力测试工具。非常简单好用。

## 安装
* CentOS7
 ```
 sudo yum install httpd-tools
 ```

* windows

    - 下载安装apache web server 安装或解压之后，在bin\目录下有个ab执行文件。
    - 打开运行--cmd 打开命令提示符，定位到bin\目录下。
    - 下载链接 http://www.apache.org/

## 基本用户

```
ab  -c  [并发用户数]  -n  [发送请求数]   [被测试页面的URL]
```


1. 假期设置一个用户一个请求，对百度首页加压： http://www.baidu.com/

```
ab -c 1 -n 1 http://www.baidu.com
```


2.同时10个连线，连续点击1000，并且使用Keep-Alive方式连线

```
ab -n 1000 -c 10 -k http://www.baidu.com
```

3.将测试中的某些数据输出到output.csv文件中
```
ab -e output.csv -n 1000 -c 10 http://www.baidu.com
```

4.设定测试时间。并发访问数为3，持续访问时间为5分钟（300秒）
```
ab -c 3 -t 300  http://www.baidu.com
```


### 参数说明
```
输入简体字,点下面繁体字按钮进行在线转Server Software:    Web主机的作业系统与版本(若Web主机设定关闭此资讯则无)；在此例中就是压力测试的对象nginx
Server Hostname:  Web主机的IP位址(Hostname)
Server Port:           Web主机的连接埠(Port)
Document Path:     测试网址的路径部分
Document Length: 测试网页回应的网页大小
Concurrency Level: 同时进行压力测试的人数
Time taken for tests: 本次压力测试所花费的总秒数                                                  ；此次压力测试花费的世间
Complete requests: 完成的要求数(Requests)
Failed requests:      失败的要求数(Requests)
Write errors:           写入失败的数量
Total transferred:   本次压力测试的总数据传输量(包括 HTTP Header 的资料也计算在内)
HTML transferred:  本次压力测试的总数据传输量(仅计算回传的 HTML 的资料)
Requests per second: 平均每秒可回应多少要求                                                     ；是否可以认为是QPS
Time per request:  平均每个要求所花费的时间(单位: 豪秒)                                    ；每次并发请求时间（所有并发）
Time per request:  平均每个要求所花费的时间，跨所有同时连线数的平均值(单位: 豪秒)    ；每一次请求时间（并发平均）
Transfer rate:         从 ab 到 Web Server 之间的网路传输速度
最后的 Connection Times (ms) 指的是压力测试时的连线处理时间：

横轴栏位的部分：

min:       最小值
mean:    平均值(正、负标准差)
median: 平均值(中间值)
max:      最大值
纵轴栏位的部分：

Connect:     从 ab 发出 TCP 要求到 Web 主机所花费的建立时间。
Processing:  从 TCP 连线建立后，直到 HTTP 回应(Response)的资料全部都收到所花的时间。
Waiting:       从发送 HTTP 要求完后，到 HTTP 回应(Response)第一个 Byte 所等待的时间。
Total:           等于 Connect + Processing 的时间（因为 Waiting 包含在 Processing 时间内了）换
```
[官方文档](http://httpd.apache.org/docs/2.0/programs/ab.html)
