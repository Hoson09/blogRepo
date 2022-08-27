---
title: vue2.0 extend && component
date: 2022-08-27 23:23:32
tags: 前端技术分享
---

# 前言:

1. 日前在工作中，遇到一个需求就是需要在一个`.vue`文件中,根据数据的变化，动态添加一个 dom，如果这个 dom 只是一个静态的 dom 标签，那么这个问题就很简单，但是需要添加的 dom 是一个带有 vue-directive 自定义指令的 dom,就不能用常规的 js 结合 dom 的语法来处理了。
2. 只能使用 createElement 渲染函数来渲染出来一个动态的具有自定义指令的虚拟 dom,然后在适当的时机渲染到页面上，现在问题是怎么渲染这个自己定义的这个虚拟 dom
3. 其实 Vue2.0 的实现方式粗略来讲，就是 Vue 把 js 文件设计的页面数据，转化成相应的虚拟 dom，然后再渲染挂载到#app 上的一个单页面工程，然后在 Vue 的工程中，使用 Vue 的 component()函数,实现页面的组件化转化。那么现在思路就是 component 和 extend，这两个函数来实现这个目标。

# 1.component(id,options) 函数：

- component:`Vue.component( id, [definition] ),注册或获取全局组件。注册还会自动使用给定的 id 设置组件的名称。获取的全局组件也是返回的是一个构造器函数,不是渲染后的 dom 本身。`

1. 定义一个名为 button-counter 的新组件

- js 函数实现部分

```js
Vue.component("button-counter", {
  data() {
    return { count: 0 };
  },
  template: `<button v-on:click="count++">You clicked me {{ count }} times.</button>`,
  /*因为已经在Vue的环境中了，
  可以使用template 模板语法，
  也可以直接使用render渲染函数，
  因为我们的需求写一个动态可以重用的小型虚拟dom，
  那么就可以直接使用render函数代替上面的内容，重用性就更高一些。*/
  render(h) {
    h(
      "button",
      {
        on: {
          click: this.count++,
        },
      },
      `You clicked me ${this.count} times.`
    );
  },
});
```

- html 部分

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

- main.js

```js
new Vue({ el: "#components-demo" });
```

# 2.extend(options) 函数

- extend:`使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象`

- html 部分

```html
<div id="mount-point"></div>
```

- js 部分

```js
// 创建构造器
var Profile = Vue.extend({
  template: "<p>{{firstName}} {{lastName}} aka {{alias}}</p>",
  data: function () {
    return {
      firstName: "Walter",
      lastName: "White",
      alias: "Heisenberg",
    };
  },
  //同理，也可以使用渲染函数直接替换上面的内容。
  render(createElement) {
    return createElement("p", `Walter White aka Heisenberg`);
  },
});
// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount("#mount-point"); //
```

# 总结：

#### 1. extend 和 component 都可以创造一个可以重用的组件，但是创造的方式和使用的场景是不同的。

#### 2. component 在创建组件的时候，已经对这个组件进行命名了，然后这个在 Vue 作用域下的所有页面中可以直接使用这个名字的组件了，但是这个函数直接帮你已经做好挂载的操作，不能自己选择挂载的时机，相对来说操作起来比较笨重，适合比较固定的大页面编写。其实根据源码，component 在实现的时候也是使用 extend 方法先创建 Vue 实例然后生成相应的虚拟 dom，然后挂载到 Vue 的全局组件的数组中以供直接使用，所以 component 其实就是封装了 extend,再帮你挂载到 Vue 全局组件数组中的函数操作,他帮你做出了决定何时挂载，你只需要引用，关注组件内部的逻辑即可。

#### 3. extend 是创建一个 Vue 实例，也就是说，创建出来的 Vue 实例形成的组件，再`new Profile().$mount("#mount-point")`挂载之前都是一个虚拟的 dom 状态，挂载操作的时机由你决定,这样你的可操作性就很大，当你需要生成一个动态的虚拟 dom 时，建议使用 extend 创建实例的方式来实现。

#### 4. 思考到这里,渐渐粗略得明白 Vue 大概实现原理其实就是把他所有的页面以及这个页面上数据的逻辑运算,都整合成了一个虚拟 dom，然后挂载（懒加载的组件暂时不挂载）到相应得真实 dom 上生成一个有一个组件，整个项目就是一个组件（或者虚拟 dom）集合,然后根据页面的逻辑顺序,对要展示的组件进行相应的展现出来的过程。然后在这些设计好的虚拟 dom 的集合基础上,再通过观察者模式双向绑定数据和 diff 算法实现监听页面数据变化,然后进行相应的局部渲染,相应的改善前端性能。
