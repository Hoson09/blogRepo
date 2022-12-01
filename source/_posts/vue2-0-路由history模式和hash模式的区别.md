---
title: vue2.0 路由history模式和hash模式的区别
date: 2022-12-01 08:59:48
tags: 前端技术分享
---

# 前言

1. vue 本质上是一个单页面应用，这个单页面应用的所有页面都是通过虚拟 dom 最终通过根 vue(app.vue)渲染到 index.html 上的, 而 vue 项目中呈现的多个页面，就是通过 vue-router 来渲染加载的。
2. vue-router 分为两种模式来呈现，history 和 hash 模式。

# 1.history 模式：

1. 外观上和传统的前端路径没什么不同，比较符合人的使用习惯，比较美观，也比较利于 SEO 的搜索推送。
2. 原理基于`html5`的`history`对象的新特性`pushState()`和`replaceState()`(主要实现依据)，以及监听`popstate`事件共同来实现的。
   - `pushState()`和`replaceState()`并不会促使页面的刷新，也不会触发`popstate`事件，只会改变 url 和记录浏览器的状态，`pushState()`是新增一个历史记录，`replaceState()`是替换当前的历史记录。
   - 使用前进后退按钮，或者调用`history`的`go()`,`back()`和`forward()`方法可以刷新页面，并触发`popstate`事件，然后在事件中再进行相应的页面跳转。

```js
window.history.pushState(state,title,path);
window.history.replaceState(state,title,path);

window.addEventListener('pushState',() = > {
    //在这里实现相应的页面内容的变化渲染
})
window.addEventListener('replaceState',() = > {
    //在这里实现相应的页面内容的变化渲染
})
```

3. 传统的前端应用，一个特定的路径下对应着服务器上的一个特定的前端 html 文件的。因为 history 模式和传统路径相同，所以在浏览器上使用传统路径搜索时，就要给服务器发送请求，寻找该路径下的对应的 html，但是 vue 项目打包存在服务器上的前端文件只有 index.html,所以这个路径在 vue 的前端项目所在的服务其上时绝对找不到的，因此会报页面 404 错误。此时需要在服务器上配置安全路径，如果输入了无法找到的路径，就默认重定向的 vue 的 index.html 上，然后这个路径进入到 index.html 上，再根据 vue-router 渲染呈现相应的页面。(服务器具体怎么配置，查询官网即可)

# 2.hash 模式：

1. 外观上与传统 url 多增加了一个#，这个把 url 主体和后面的路径以及参数分隔开，不美观，也不利于后期的 SEO 搜索，
2. 原理就是基于监听`hashChage`事件，然后通过`location.hash`值来进行相应的页面渲染和切换。

```js
window.addEventListener('replaceState',() = > {
    // 获取当前url的哈希值
    const _hash = location.hash
    // 根据不同的哈希值显示不同的内容
    switch(_hash) {
         case '/#a':
            //在这里实现相应的页面内容的变化渲染
            break;
         case '/#b':
           //在这里实现相应的页面内容的变化渲染
            break;
         case '/#c':
            //在这里实现相应的页面内容的变化渲染
            break;
        default：
            break;
    }
})
```

3.  hash 模式因为#把 url 的接口和#号后面的路径参数一分为二，所以这种模式不会刷新页面，也不会向服务器发送请求寻找特定 html 文件。hash 模式实现原理比较简单，可以适配各种环境和浏览器版本，也无需进行服务器上的适配。

# 3. 总结

1. hash 和 history 模式的实现过程中，都是尽量不刷新页面，不向服务器发送请求，都在单页面文件 index.html 中实现的渲染过程。Vue-router 配合 Vue 的虚拟 Dom，可以极大的提升前端页面加载的性能。
2. 虽然 hash 样式上多了一个#，不美观且有损 SEO 搜索的效率，但是因为 hash 使用的是老特性，所以 hash 模式可以适配低版本的浏览器和低版本的 html 页面，并且无需配置服务器定向路径。
3. 虽然 history 在习惯上更符合人的使用传统有利于 SEO，但是实现原理是 Html5 才赋予的新特性，所以他无法适配低版本浏览器，并且 history 需要在服务器上进行适配。
