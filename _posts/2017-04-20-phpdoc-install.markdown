---
layout: post
title:  " PHPDOC自动生成文档"
date:   2017-04-20 18:11:03 +0800
categories: jekyll update
---

# phpdoc

phpdoc site [phpdoc](https://www.phpdoc.org/)

## 全局 install

```
composer gobal require phpdocumentor/phpdocumentor
```

## 开发 install

```json
{
  "require-dev":{
    "phpdoucmentor/phpdoucmentor":"2.*"
  }
}
```

### 生成文档
```php
phpdoc -d "绝对路径"

phpdoc -d "D:\phpdoc\1" -t "D:\phpdoc\1"  -o "HTML:frames:phpedit"
```

## 使用说明

### @api
examples
```php
/**
 * This method will not change until a major release.
 *
 * @api
 *
 * @return void
 */
 function showVersion()
 {

 }
```

### @author
examples
```php
/**
 * @author My Name
 * @author My Name <my.name@example.com>
 */
```

...
