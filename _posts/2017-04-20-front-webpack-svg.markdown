---
layout: post
title:  "结合 webpack 2 使用 SVG Sprite"
date:   2017-04-20 19:12:03 +0800
categories: jekyll update
---
# 结合 webpack 2 使用 SVG Sprite

# 优点 & 缺点

  优点： * 将多个请求压缩为无请求 * svg 对比 image 其屏幕适应性更好，任何分辨率都能达到高清效果 * svg 体积更小 * 每一个 <symbol> 都可以重复利用

  缺点： * svg 不利于变动性大的图片，例如需要经常修改颜色 * 兼容性对于需要兼容 IE8- 的网站不好，需要对低版本浏览器有替代方案

# 步骤

## 第一步
安装npm 包
```npm
npm install svg-sprite-loader
npm install svgo-loader
```

## 第二步
修改webpack配置文件,引入svgo-loader 和 svg-sprite-loader
webpack.config.js

```js
var webpack = require('webpack')
var path = require('path')


var svgoConfig = JSON.stringify({
  plugins: [
    {removeTitle: true},
    {convertColors: {shorthex: false}},
    {convertPathData: false}
  ]
});

module.exports = {
  entry: './app/main.js',
  output: {
    path: path.resolve(__dirname, './build'),
    publicPath: '/',
    filename: 'build.js'
  },
  resolve: {
    modules: [
      'node_modules',
	    path.resolve(__dirname, 'app/utils')
    ],
    alias: {
      'font': path.resolve(__dirname, 'app/assets/fonts'),
      'actions': path.resolve(__dirname, 'app/vuex/actions.js'),
      'ui': path.resolve(__dirname, 'app/components/ui'),
      'assets': path.resolve(__dirname, 'app/assets'),
      'domain': path.resolve(__dirname, 'app/config/domain.js'),
	    'utils': path.resolve(__dirname, 'app/utils')
    },
    extensions: ['.json', '.js', '.vue']
  },
  module: {
    loaders:[
      {
        test: /\.js$/,
        loader: 'babel',
        exclude: /(node_modules|bower_components)/,
        query: {
          presets: ['stage-0', 'es2015'], // stage-0 is es7 rule , ex:like {...state}
        },
      },
      {
        test: /\.json$/,
        loader: 'json'
      },
      {
        test: /\.vue$/,
        loader: 'vue',
      },
      {
        test: /\.(png|jpg|gif)$/,
        loader: 'file?name=[name].[ext]?[hash]'
      },
      //此处添加
      { test: /\.svg$/,
        loaders: [ 'svg-sprite-loader',  'svgo-loader?' + svgoConfig]
      },
      { test: /\.(woff|woff2|ttf|eot)(\?v=[0-9]\.[0-9]\.[0-9])?$/, loader: 'url?limit=10000&name=fonts/[hash:8].[name].[ext]'},
    ],
    noParse: [],
  },
  plugins: [
    new webpack.ProvidePlugin({
      jQuery: 'jquery', // 这个可以使jquery变成全局变量
      $: 'jquery',
    }),
    new webpack.LoaderOptionsPlugin({
      vue: {
        postcss: [
          // 让autoprefixer 对 import '*.scss' 也生效
          require('postcss-import')({
            addDependencyTo: webpack
          }),
          require('autoprefixer')({
            browsers: ['last 3 versions'],
          }),
          require('cssnano')({ safe: true })
        ],
        loaders: {
          isass: 'vue-style!css!sass?indentedSyntax',
          scss: 'vue-style!css!sass',
          sass: 'vue-style!css!sass'
        }
      }
    })
  ]
}

```

## 第四步
添加svg.js文件 ,同时引入到项目中

```
  import from 'svg.js'
```

```js
import analytics from '../svg/analytics.svg'
import businessman from '../svg/businessman.svg'
import businessmen from '../svg/businessmen.svg'
import certificate from '../svg/certificate.svg'

import chat from '../svg/chat.svg'
import contract from '../svg/contract.svg'
import mebuDemand from '../svg/mebuDemand.svg'
import menuCustom from '../svg/menuCustom.svg'
import menuDashboard from '../svg/menuDashboard.svg'
import menuFinance from '../svg/menuFinance.svg'
import menuMessage from '../svg/menuMessage.svg'
import menuSettings from '../svg/menuSettings.svg'
import menuTenant from '../svg/menuTenant.svg'

```



## 第五步
在项目中使用

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>svg-sprites-demo</title>
  </head>
  <body>
    <div class="icons">
      <svg><use xlink:href="#analytics"/></svg>
      <svg><use xlink:href="#archives"/></svg>
      <svg><use xlink:href="#businessman"/></svg>
      <svg><use xlink:href="#businessmen"/></svg>
      <svg><use xlink:href="#certificate"/></svg>
      <svg><use xlink:href="#chat"/></svg>
      <svg><use xlink:href="#contract"/></svg>
    </div>
  </body>
</html>
```
