---
layout: post
title:  "shopnc开发手册"
date:   2017-09-15 19:56:00 +0800
categories: jekyll update
---



# shopNC B2B2C


## 环境&配置

* php5.3以上 （官方建议使用php5.4）
* Nginx/ Appache
* Mysql


PHP配置文件php.ini
```
extension=php_curl.dll
extension=php_openssl.dll
date.timezone =PRC
session.auto_start = 1
asp_tags = Off // Allow ASP-style <% %> tags
short_open_tag = On
```

## 部署&安装

1. 导入数据库脚本

2. 设置配置文件 [打开\data\config\config.ini.php ]
```config
$config['db']['1']['dbhost']       = '127.0.0.1'; // 使用phpstudy情况下建议使用127.0.0.1。
$config['db']['1']['dbport']       = '3306';
$config['db']['1']['dbuser']       = 'root';
$config['db']['1']['dbpwd']        = 'root';
$config['db']['1']['dbname']       = 'shopnc_db';
```
3. linux请查看权限

```config
/data/cache
/data/config
/data/log
/data/session
/data/resource/phpqrcode/temp
/data/upload
/sql_back
/install


// 设置权限
chmod -R 777 /data/cache

```

4. 登陆管理员后台，清空网站所有缓存
```
地址：http://本地域名/admin
账号：admin
密码：123456
```


## 商城目录结构
```ssh
├─index.php……………………………………………………………………入口文件
├─global.php……………………………………………………………………全局变量文件
├─admin……………………………………………………………………后台管理目录
│  ├─config
│  ├─control……………………………………………………………………控制器目录
│  ├─include
│  ├─language……………………………………………………………………语言包目录
│  │  ├─zh
│  │  └─zh_cn
│  ├─resource……………………………………………………………………js，css font
│  │  └─js
│  └─templates……………………………………………………………………模板目录
│      ├─default
│      │  ├─css
│      │  ├─images
│      │  └─layout
│      └─images
├─chat……………………………………………………………………IM目录
├─circle……………………………………………………………………圈子目录
├─cms……………………………………………………………………CMS目录
├─core…………………………………………………………………… 框架目录
│  ├─framework……………………………………………………………………系统框架
│  │  ├─cache……………………………………………………………………缓存驱动
│  │  ├─core……………………………………………………………………核心文件
│  │  ├─db……………………………………………………………………数据层驱动
│  │  ├─function……………………………………………………………………系统方法
│  │  └─libraries……………………………………………………………………类库
│  └─language……………………………………………………………………框架语言包目录
├─crontab……………………………………………………………………计划任务
│  ├─config
│  └─control
├─data……………………………………………………………………公共资源目录
│  ├─api……………………………………………………………………第三方API目录
│  ├─cache……………………………………………………………………缓存文件目录
│  ├─config……………………………………………………………………配置文件目录
│  ├─crontab……………………………………………………………………定时触发文件目录
│  │  └─include
│  ├─log……………………………………………………………………系统运行日志目录
│  ├─session……………………………………………………………………SESSION文件存放目录
│  ├─smilies
│  ├─uc_client……………………………………………………………………ucenter目录
│  ├─upload……………………………………………………………………上传图片目录
├─delivery……………………………………………………………………物流自提点
├─im……………………………………………………………………会员聊天
├─microshop……………………………………………………………………microshop 微商城目录
├─mobile……………………………………………………………………mobile 手机客户端API
├─queue……………………………………………………………………手机端和app接口目录
├─shop……………………………………………………………………pc端
├─wap……………………………………………………………………wap商城
└─

```

## 路由分析

**http://本地域名/shop/index.php?act=brand&op=list&brand=89**

可拆分

命名 | 作用
---|---
shop | 模块
index.php | 统一入口文件
act | 控制器Controller
op | 方法Function
&= | url参数




## MVC 结构

shopnc 常见的mvc结构。每个模块对应自己的Controller层，template View层。Model为全局共享

```
数据模型(Model)  /data/model
视图(View)  /shop/templates
控制器(Controller) /shop/control
```

### Model 层
* 实例化商品模型
```php
    $goods = Model('goods');
```

系统首先会查找data/model/goods.model.php文件及内部的memberModel类是否存在，如果存在会查找core\framework\libraries\model.php

### Controller 层

在shopnc里，基本每个controller 都会继承上级对应基础功能的Controller，方便使用相对应的方法以及数据传递。


* BaseMemberControl
```php
class BaseMemberControl extends Control {
    protected $member_info = array();   // 会员信息
    public function __construct(){

        if(!C('site_status')) halt(C('closed_reason'));

        Language::read('common,member_layout');

        if ($_GET['column'] && strtoupper(CHARSET) == 'GBK'){
            $_GET = Language::getGBK($_GET);
        }
        //会员验证
        $this->checkLogin();
        //输出头部的公用信息
        $this->showLayout();
        Tpl::setDir('member');
        Tpl::setLayout('member_layout');

        //获得会员信息
        $this->member_info = $this->getMemberAndGradeInfo(true);
        $this->member_info['voucher_count'] = Model('voucher')->getCurrentAvailableVoucherCount($_SESSION['member_id']);
        $this->member_info['redpacket_count'] = Model('redpacket')->getCurrentAvailableRedpacketCount($_SESSION['member_id']);
        Tpl::output('member_info', $this->member_info);

        // 常用操作及导航
        $menu_list = $this->_getNavLink();

        //系统公告
        $this->system_notice();

        // 交易数量提示
        $this->order_tip();

        // 页面高亮
        Tpl::output('act', $_GET['act']);
    }

}
```

* memberControl
```php
class memberControl extends BaseMemberControl{

    /**
     * 我的商城
     */
    public function homeOp() {
        Tpl::showpage('member_home');
    }
}

```


## 系统函数&系统类&第三方工具类

框架的核心部分

```
├─core…………………………………………………………………… 框架目录
│  ├─framework……………………………………………………………………系统框架
│  │  ├─cache……………………………………………………………………缓存驱动
│  │  ├─core……………………………………………………………………核心文件
│  │  ├─db……………………………………………………………………数据层驱动
│  │  ├─function……………………………………………………………………系统方法
│  │  └─libraries……………………………………………………………………类库
│  └─language……………………………………………………………………框架语言包目录
```

```php

 // 时间插件
import('function.datehelper');
$year_arr = getSystemYearArr();

// Excel 工具类
import('libraries.excel');
$excel = new Excel();

```

## 加载语言包

```php
// 引入语言包文件
Language::read('member_home_member');

// 获取所有语言参数
$lang   = Language::getLangContent();

// 保存
$save_succ = $lang['nc_common_save_succ'];

```

## 模板Template

### PHP PC端模板

```php
<?php

defined('InShopNC') or exit('Access Invalid!');?>

<!--上面是进行合法验证，在商城系统下的模板文件都需要进行该验证。-->

<?php include template('home/cur_local');?>

<!--上面是在品牌展示页面显示当前位置，实际输出内容为：当前位置：首页 > 所有品牌 -->


<h2><b><?php echo $lang['brand_index_brand_list'];?></b></h2>

<!--上面是输出标题，实际内容为：品牌列表-->

<!--$lang 数组内容为Language::read() 得到的语言包信息，-->

<!--使用Tpl::output抛出到模板的内容，保存到了$output数组中，保存下标名称为output方法的第一个参数值,-->
<?php if(is_array($output['brand_list'])){?>
    <?php foreach($output['brand_list'] as $key=>$val){?>
        <!--循环输出品牌内容-->
        <div class="wrap">
            <ul>
                <li>
                    <a href="<?php echo ncUrl(array('act'=>'brand','op'=>'list','brand'=>$brand['brand_id']));?>" >
                        <!--生成跳转URL（伪静态或动态）-->
                        <span><img src="<?php echo ATTACH_BRAND.'/'.$brand['brand_pic'];?>" alt="<?php echo $brand['brand_name'];?>" /></span>
                    </a>
                </li>
            </ul>
        </div>
    <?php }?>
<?php }?>


```

### 手机端JAVASCIRPT模板


#### javascript 模板引擎基本原理

虽然每个引擎从模板语法、语法解析、变量赋值、字符串拼接的实现方式各有所不同，但关键的渲染原理仍然是动态执行 javascript 字符串。


#### 功能概述

提供一套模板语法，用户可以写一个模板区块，每次根据传入的数据，生成对应数据产生的HTML片段，渲染不同的效果。

#### 特性

* 模版编译，渲染
* 支持所有主流浏览器及Node（UMD）
* JavaScript原生语法
* 丰富的自定义配置
* 支持数据过滤
* 异常捕获功能
* 功能专一，简单好用

[git](https://github.com/yanhaijing/template.js)
[demo](https://github.com/yanhaijing/template.js/tree/master/demo)


```html
<!doctype html>
<html>
<head>
    <meta charset="UTF-8">
    <title>include demo</title>
    <script src="template.js"></script>
</head>
<body>
<div id="content"></div>
<script id="customTag" type="text/html">
    <h1><%= header%></h1>
    <ul>
        <% for(var i=0; i<tag.length; i++){ %>
            <li>条目内容<%= i+1 %> : <%= tag[i] %></li>
        <% } %>
    </ul>
</script>
<script>
    template.openTag = '<%';
    template.closeTag = '%>';
    var listdata = {
        header : 'your study list',
        tag : ['算法导论','php','c#','jquery','vue.js','python']
    };
    var resulthtml = template.render ('customTag', listdata);
    document.getElementById('tagcontent').innerHTML = resulthtml;
</script>
</body>
</html>
```


## 系统常量

```php
[ProjectName]=> 项目名称
[BUILDCORE]=> 是否压缩框架（true/false）
[BasePath]=> 系统所在基准目录（如 /var/www/shopnc）
[DS] => 目录分隔符 /
[InShopNC]=> 合法调用判断标志
[StartTime]=> 系统开始运行时间（如1350096132.6587）
[TIMESTAMP]=> 系统当前时间（如1350096132）
[RUNCOREPATH]=>压缩后的框架所在目录 (如 /var/www/shopnc/cache/~shopnc.php)
[SiteUrl]=> 商城URL地址（http://www.shopnc.net）
[CHARSET]=> 系统编码（UTF-8/GBK）
[DBDRIVER]=> 数据库访问驱动（mysqli、mysql、pdo_mysql、oci8）
[SESSION_EXPIRE]=> 缓存周期，单位（秒）
[LANG_TYPE]=> 语言包，默认简体中文zh_cn
[COOKIE_PRE]=> cookie前缀，系统自动生成
[CORE_PATH]=> 框架所在目录(如 /var/www/shopnc /framework)
[TPL_NAME]=> 模板风格，默认default
[BASE_TPL_PATH]=> 模板文件目录(如 /var/www/shopnc /templates/default)
[RESOURCE_PATH]=> 外部资源目录地址（如 http://www.shopnc.net/resource）
[DBPRE] =>数据库表前缀 默认shopnc_
[ATTACH_PATH]=> 附件目录，默认upload
[ATTACH_COMMON]=> 通用附件目录 默认upload/common
[ATTACH_AVATAR]=> 头像目录 默认 upload/avatar
[ATTACH_STORE]=> 店铺信息目录 默认 upload/store
[ATTACH_GOODS]=> 店铺产品目录 默认 upload/store/goods
[ATTACH_AUTH]=> 店铺证件目录 默认 upload/auth
[ATTACH_MOBILE]=> 手机客户端内容目录 默认upload/mobile
[ATTACH_LINK]=> 友情链接logo目录 默认upload/link
[ATTACH_ARTICLE]=> 文章附件目录 默认 upload/article
[ATTACH_BRAND]=> 品牌附件目录 默认 upload/brand
[ATTACH_ADV]=> 广告图片目录 默认 upload/adv
[ATTACH_ACTIVITY]=> 促销活动目录 默认 upload/activity
[ATTACH_COUPON]=> 优惠券目录 默认 upload/coupon
[ATTACH_WATERMARK]=> 水印图片目录 默认 upload/watermark
[ATTACH_POINTPROD]=> 积分商品图片目录 默认 upload/pointprod
[ATTACH_SPEC]=> 自定义规格图片目录 默认 upload/spec
[ATTACH_GROUPBUY]=> 团购图片目录 默认 upload/groupbuy
[ATTACH_SLIDE]=> 店铺二维码目录 默认 upload/store/slide
[ATTACH_VOUCHER]=> 代金券模板目录 默认upload/voucher
[TEMPLATES_PATH]=> 模板URL访问地址（如http://www.shopnc.net/templates/default）
[MD5_KEY]=> MD5_KEY值 系统自动生成

```


## 定时任务
```
crontab -e
# Linux下定时任务设置示例
# php安装路径 /usr/local/php
# 商城安装路径 /wwwroot/shopnc

# 执行频率：10分钟，[更新商品促销状态、首页商品信息、邮件通知等]
*/10 * * * * /usr/local/php/bin/php /wwwroot/shopnc/crontab/index.php minutes
# 执行频率：1小时，[更新全文索引]
0 */1 * * * /usr/local/php/bin/php /wwwroot/shopnc/crontab/index.php hour
# 执行频率：1天，[订单超期自动处理、售后超时处理、代金券虚拟码等过期提醒、更新统计等]
0 3 * * * /usr/local/php/bin/php /wwwroot/shopnc/crontab/index.php date
# 执行频率：1个月，[生成结算]
0 4 1 * * /usr/local/php/bin/php /wwwroot/shopnc/crontab/index.php month
```

## 内置ORM


## 日志

系统内置一个简单日志类。用来添加日志方便记录信息。


使用方法：

```

$message = 'test';
$level = 'err'; // err or sql

Log::record($message,$level);
```

生成文件:

data/log/20170918.log


## 二次开发规范

### 关于代码层面

1. 代码标记建议使用标准形式（<?php…… ?>），不建议使用简写形式（<? …… ?>），如果服务器未开启对短标记的支持（short_open_tag = Off），将会有暴露源码的危险。
2. 模板中输出变量使用“echo” 输出,以分号结尾，不建议使用“=”输出。
```php
$color = "red";
<p>Roses are <?=$color?></p> // 不建议使用
```
3. 对于函数级、类类及文件头注释使用C样式的注释“/* */”，对于代码行（段）的注释可使用标准C++注释”//”。

4. 建议使用缩进保持代码的层级关系，每个缩进的单位约定是一个TAB(4个空白字符宽度)，开发工具建议使用Zend Studio、UltraEdit、EditPlus等编辑器。

5. 关于PHP变量的命名规则，建议以英文拼写为蓝本，遵循简洁和可读性原则。
```
$store_info = [];
```

6. 全局常量的定义要统一使用大写，单词之间使用下划线连接。

7. 开发时，代码的error_reporting报错级别建议设置为E_ALL & ~E_NOTICE。

8. Include，require包含文件时建议使用绝对路径，框架底层引入文件可使用Include，require（不带once效率高一些），在业务逻辑层引入文件时可使用Include_once，require_once。
```php
// 本框架使用
import('function.datehelper');

```


### 关于数据库

1. 表和字段命名同样应以英文拼写为蓝本，遵循简洁和可读性原则，长度适中，建议长度在30个字符内，否则跨库迁移可能会存在麻烦。
2. 字段类型设置应遵循足够用不浪费的原则。
3. SQL语句中，除了表名、字段名称以外，全部语句和函数均应大写。
4. 尽可能避免直接编写SQL语句去执行，提倡使用系统数据库操作层相应入口来完成。
5. 索引可以加快检索速度，但不易过多，单个表建议不超过5个索引。

### 关于目录的安全

1. 文件与目录命名均使用小写字母，php文件后缀均为“.php”，
2. 请在敏感目录中放置一个1字节的index.html文件，内容为一个空格，以避免当http服务器的Directory Listing打开时，服务器文件被索引和列表。

```



[开发规范参考网上]