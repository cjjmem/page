---
layout: post
title:  " Laravel搭建swagger+dinggo+jwt"
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
 }

 /**
   * @SWG\POST(path="/api/login",
   *   tags={"坐席管理"},
   *   summary="坐席登录",
   *   description="登录",
   *   operationId="login",
   *   produces={"application/json"},
   *   @SWG\Parameter(
   *     in="query",
   *     name="name",
   *     type="string",
   *     description="坐席名称",
   *     required=true,
   *   ),
   *   @SWG\Parameter(
   *     in="query",
   *     name="password",
   *     type="string",
   *     description="坐席密码",
   *     required=true,
   *   ),
   *   @SWG\Parameter(
   *     in="query",
   *     name="ext_name",
   *     type="string",
   *     description="分机号",
   *     required=true,
   *   ),
   *   @SWG\Response(response="default", description="操作成功")
   * )
   */
   public function login() {

   }
}
```
`
路由 层面
```
// swagger ui
Route::group(['prefix' => 'swagger'], function () {
    Route::get('json', 'SwaggerController@getJSON');
});
```


[dinggo参考](https://github.com/dingo/api/blob/master/readme.md)
[jwt参考](http://laravelacademy.org/post/3640.html?utm_source=tuicool&utm_medium=referral)
[jwt git](https://github.com/tymondesigns/jwt-auth/blob/develop/README.md)
[swaager 参考](https://github.com/zircote/swagger-php)
[swaager git](https://github.com/zircote/swagger-php)
