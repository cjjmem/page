---
layout: post
title:  "laravel记录Sql日志的方法"
date:   2017-05-25 12:39:03 +0800
categories: jekyll update
---

# laravel记录Sql日志的方法

在项目开发过程中或者是性能优化中，经常会有要查看执行sql的情况，然而Laravel日志默认不记录执行sql。然而并不担心，强大Laravel的提供者帮助我们解决这个问题。下面是我总结的几种方式：

    1.Laravel中的事件监听
    2.Laravel中的服务提供者
    3.第三方组件的调试工具


## 方法一:Laravel中的事件监听

1. 在 App\Providers\EventServiceProvider:class 中的$listen中新增如下

```php
protected $listen = [
    'App\Events\Event' => [
        'App\Listeners\EventListener',
    ],
    // 新增SqlListener监听QueryExecuted
    'Illuminate\Database\Events\QueryExecuted' => [
        'App\Listeners\SqlListener',
    ],
];
```

2. 通过执行命令自动生成App\Listeners\SqlListener文件

```
php artisan event:generate
```

手动添加App\Listeners\目录中 SqlListener.php 文件

```php
namespace App\Listeners;

use Illuminate\Database\Events\QueryExecuted;

class SqlListener {
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct() {
        //
    }

    /**
     * Handle the event.
     *
     * @param  =QueryExecuted  $event
     * @return void
     */
    public function handle(QueryExecuted $event) {
        // 在这里编写业务逻辑
    }
}
```

3.在handle方法中编写记录sql的业务逻辑，如：

```php
use Illuminate\Support\Facades\Log;

public function handle(QueryExecuted $event)
{
    Log::Info($event->sql);
}

```

4.调试是否成功

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use Illuminate\Support\Facades\DB;

class IndexController extends Controller
{
    public function index(){
        $users = DB::table('users')->get();
        dd($users);
    }
}
```

5.查看日志文件 storage/logs/..

```log
[2017-05-25 03:06:31] local.INFO: select * from `users`
```

### 方法二:Laravel中的服务提供者


### 方法三:第三方组件的调试工具

