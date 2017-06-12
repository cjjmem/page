---
layout: post
title:  "vue1.x vue2.0中如何使用nprogress组件"
date:   2017-05-27 18:00:03 +0800
categories: jekyll update
---

# nprogress 前端页面加载进度条神奇

[nprogress演示](http://ricostacruz.com/nprogress/)

## vue1.x中使用nprogress

安装
```
npm install nprogress --save
```

main.js

```es6
import Vue from 'vue'
import VueRouter from 'vue-router'
import NProgress from 'nprogress'

Vue.use(VueRouter)

const router = new VueRouter({
  transitionOnLoad: true,
  saveScrollPosition: true
})

router.beforeEach(({ next }) => {
  NProgress.start()
  next()
})

router.afterEach(() => {
  NProgress.done()
})

```

在Sass文件中引入样式表
```
@import '~nprogress/nprogress.css';
```

## vue2中使用nprogress

main.js

```
import Vue from 'vue'
import 'nprogress/nprogress.css'

import NProgress from 'nprogress'
import App from './App.vue'

const = new Router({
  routes: [
    {
      path:'/',
      components: App,
      name:'app'
    },
  ]
})

NProgress.configure({ showSpinner: false }); //进度条配置

router.beforeEach((to, from, next) => {
  NProgress.start();
  next()
})
router.afterEach(transition => {
  NProgress.done();
});

new Vue({
  el: '#app',
  router,
  render: h => h(App)
})

```

