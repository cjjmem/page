---
layout: post
title:  " 前端权限之-Vue篇"
date:   2017-04-20 19:11:03 +0800
categories: jekyll update
---

# vue前端权限控制

html
```html
<div id="app" class="container">
  <ul>
    <li v-permission="'see-home'"><a href="#">Home</a></li>
    <li v-permission="'see-contact'"><a href="#">Contact</a></li>
    <li v-permission="'see-about'"><a href="#">About</a></li>
    <li v-permission="'see-product'"><a href="#">Product</a></li>
  </ul>
</div>
```

js
```js
var store = {
  state: {
    user: {
      id: 1,
      name: 'Joe',
      email: 'joe@budden.com',
      perms: [
        {
          id: 1,
          name: 'see-product'
        },
        {
          id: 2,
          name: 'see-home'
        }
      ]
    }
  },
  hasPerm: function(value)
  {
    var perms = store.state.user.perms;
    var found = false;

    perms.map(function(perm)
    {
      if (perm.name === value)
      {
        found = true;
      }
    });

    return found;
  }
};

Vue.directive('permission', {
  update: function(value)
  {
    if (store.hasPerm(value)) $(this.el).show();
    if (! store.hasPerm(value)) $(this.el).hide();
  }
});

new Vue({
  el: '#app',

  data: function()
  {
    return {

    }
  }

});

```


[参考http://codepen.io/anon/pen/aJpLZN]

