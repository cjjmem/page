---
layout: post
title:  " Laravel开发笔记"
date:   2017-04-20 18:11:03 +0800
categories: jekyll update
---

# 安装dinggo

## 修改composer.json
```
"require": {
    "dingo/api": "1.0.*@dev"
}
```
或者
```
composer require dingo/api:1.0.x@dev
```

## 添加app.php 配置文件
```
 'providers' => [
    Dingo\Api\Provider\LaravelServiceProvider::class
 ]

```

## 执行命令生成api.php

```
php artisan vendor:publish --provider="Dingo\Api\Provider\LaravelServiceProvider"
```

## 配置.env文件
```
API_STANDARDS_TREE=vnd
API_PREFIX=api
API_VERSION=v1
API_DEBUG=true
```

访问域名，会看到返回信息。
## 配置路由
```
$api = app('Dingo\Api\Routing\Router');
$api->version('v1', function ($api) {
    $api->post('/register','App\Http\Controllers\MyController@register');
});
```

## 用postman测试:domain/api/register



# 安装 jwt
```
composer require tymon/jwt-auth 0.5.*
```

## 添加app.php 配置
```
'providers' => [
   Tymon\JWTAuth\Providers\JWTAuthServiceProvider::class
]

'aliases' => [
  'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class,
  'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class
]
```

## 执行命令生成jwt.php 配置文件
```
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\JWTAuthServiceProvider"
```

## 最后生成密钥
```
php artisan jwt:generate
```

## 使用说明
Controller 层面
```php
//登录获取token
public function login(Request $request) {
    $credentials = $request->only('name', 'password');
    try {
        if (! $token = JWTAuth::attempt($credentials)) {
            $response = array(
                'error' => '用户名或密码错误',
                'status' => 401
            );
            return response()->json($response);
        }
    } catch (JWTException $e) {
        $response = array(
            'error' => '创建token时出错',
            'status' => 500
        );
        return response()->json($response);
    }
    return response()->json(compact('token'));
}

// 通过token 获取用户信息
public function getUserInfo(Request $request){
    try{
        if (! $user = JWTAuth::parseToken()->authenticate()) {
            return response()->json(['user_not_found'], 404);
        }
    } catch (Exception $e){
        return response()->json(['token_absent'], $e->getStatusCode());
    }

    return response()->json(compact('user'));
}

```


中间件 层面
```php
$api = app('Dingo\Api\Routing\Router');
$api->version('v1', function ($api) {
    $api->post('/login','App\Http\Controllers\AgentController@login');
    $api->post('/register','App\Http\Controllers\AgentController@register');

    // 中间件
    $api->group(['middleware' => 'jwt.auth'], function($api) {
        $api->get('/getUserInfo','App\Http\Controllers\AgentController@getUserInfo');
    });

});

```

路由 层面
```php
protected $routeMiddleware = [
  'jwt.auth' => \Tymon\JWTAuth\Middleware\GetUserFromToken::class,
  'jwt.refresh' => \Tymon\JWTAuth\Middleware\RefreshToken::class,
]
```


# 安装swagger
```
composer require zircote/swagger-php
composer global require zircote/swagger-php
```

## 添加界面
从[swagger-ui](https://github.com/swagger-api/swagger-ui) dist目录 复制到laravel项目目录中的 public/swagger-ui

访问
http://localost:8000/swagger-ui/


## 示例
Controller 层面
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;

class SwaggerController extends Controller
{

 /**
  * @SWG\Swagger(
  *   @SWG\Info(
  *     title="呼叫中心API文档",
  *     version="1.0.0"
  *   )
  * )
  */
 public function getJSON()
 {
     $swagger = \Swagger\scan(app_path('Http/Controllers/'));
     return response()->json($swagger, 200);
 }# HTTP路由实例教程（三）—— CSRF攻击原理及其防护

  某些路由需要避过laravel Middleware 的中间件VerifyCsrfToken

  ```php
  namespace App\Http\Middleware;

  use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as BaseVerifier;

  class VerifyCsrfToken extends BaseVerifier
  {
      /**
       * The URIs that should be excluded from CSRF verification.
       *
       * @var array
       */
      protected $except = [
          'callback',
          'ivr'
      ];
      //路由 http://127.0.0.1/callback
  }
  ```

  # 文件上传案列

  confing/filesystems.php 配置层
  ```
  <?php

  return [
      'default' => 'local',
      'cloud' => 's3',
      'disks' => [
          'local' => [
              'driver' => 'local',
              'root' => storage_path('app'),
          ],
          'public' => [
              'driver' => 'local',
              'root' => storage_path('app/public'),
              'visibility' => 'public',
          ],
          // 添加配置
          'uploads' => [
              'driver' => 'local',
              // 文件将上传到storage/app/uploads目录
              'root' => storage_path('app/uploads'),
              // 文件将上传到public/uploads目录 如果需要浏览器直接访问 请设置成这个
              //'root' => public_path('uploads'),
          ],
      ],
  ];
  ```

  view 视图层
  ```
  <form method="post" enctype="multipart/form-data" >
      <input type="file" name="picture">
      <button type="submit"> 提交 </button>
  </form>
  ```

  Controllers 层
  ```

  public function file(Request $request){
      if ($request->isMethod('post')) {
          $file = Input::file('file');
  //            $file->getPathname(),
  //            $file->getClientOriginalName(),
  //            $file->getClientMimeType(),
  //            $file->getClientSize(),
  //            $file->getError(),
          if($file->isValid()){
              //获取文件格式
              $entension = $file -> getClientOriginalExtension();
              // 设置文件名
              $filename = date('Y-m-d-H-i-s') . '-' . uniqid() . '.' . $entension;
              // 使用我们新建的uploads本地存储空间（目录）
              $realPath = $file->getRealPath();   //临时文件的绝对路径
              $bool = Storage::disk('uploads')->put($filename, file_get_contents($realPath));
              if($bool)
              //上传成功
      }

  }



  ```

  # 页面错误信息显示
  ```
  示例1

      return redirect()->back()->withInput()->withErrors('密码有误！');

      @if (count($errors) > 0)
          <div class="tools-alert tools-alert-red">
              <strong>错误</strong>你填写数据有问题！请重新填写
                  @foreach ($errors->all() as $error)
                      <p class="text-danger">{{ $error }}</p>
                  @endforeach
          </div>
      @endif



  示例2

      return redirect()->back()->with('status', 'Update Success! 成功！ :)');

      @if (session('status'))
          <div class="tools-alert tools-alert-green">
              {{ session('status') }}
          </div>
      @endif


  ```
