---
layout: post
title:  " Laravel-Excel功能"
date:   2017-04-20 18:11:03 +0800
categories: jekyll update
---
# 安装Laravel-Excel
```
composer require maatwebsite/excel ~2.1.0
```

## 添加app.php 配置文件
```
 'providers' => [
    Maatwebsite\Excel\ExcelServiceProvider::class
 ]

 'aliases' => [
   'Excel' => Maatwebsite\Excel\Facades\Excel::class,
 ]

```

## 执行命令生成excel.php

```
php artisan vendor:publish
```

## 导出参数设置
```
'import'     => [

       /*
       |--------------------------------------------------------------------------
       | Has heading
       |--------------------------------------------------------------------------
       |
       | The sheet has a heading (first) row which we can use as attribute names
       |
       | Options: true|false|slugged|slugged_with_count|ascii|numeric|hashed|trans|original
       |
       */

       'heading'                 => 'true',
   ]
```

## 示例
```
use Excel;

// 导出
public function export()
{
  $cellData = [
      ['学号','姓名','成绩'],
      ['10001','AAAAA','99'],
      ['10002','BBBBB','92'],
      ['10003','CCCCC','95'],
      ['10004','DDDDD','89'],
      ['10005','EEEEE','96'],
  ];
  Excel::create('student',function($excel) use ($cellData){
      $excel->sheet('score', function($sheet) use ($cellData){
          $sheet->rows($cellData);
      });
  })->export('xlsx');
}

// 导入
pubilc function import(){
  $filePath = 'storage/exports/test.xlsx';
  Excel::load($filePath, function($reader) {
      $data  = $reader->all();
      dd($data);
  });
}
```


[Laravel-Excel git](https://github.com/tymondesigns/jwt-auth/blob/develop/README.md)
[Laravel-Excel 参考](https://github.com/zircote/swagger-php)
