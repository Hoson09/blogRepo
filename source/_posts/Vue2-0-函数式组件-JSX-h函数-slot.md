---
title: Vue2.0 函数式组件&JSX&h函数&slot
date: 2022-06-28 22:23:13
tags: 前端技术分享
---

# 前言：

1. 之前做过一个 Vue JSX 和函数式编程组合应用，使组件的部分 Dom 的具体实现可以变的更加灵活[（# Vue JSX 和 函数式组件）。](https://juejin.cn/post/7035968044128010277)但是在实际使用中发现 JSX 的组件和 Vue 的函数式编程，只能做一些比较简单的 DOM 操作兼容性不好，如果需要在这个函数式组件中，进行一些复杂的点击事件或者逻辑判断交互时，就会有一些力不从心。随着对 Vue 的持续了解，原来不太懂的 h 渲染函数也有了一些新的理解。发现其实 h 渲染函数和函数时组件结合使用，是可以满足我对于这个组件的一些复杂逻辑的操作和点击事件的处理。
2. 当然在实际工作中，还会遇到一些 DOM 嵌套之后数据处理的问题，因为 JSX 的组件在 Vue 2.0 版本中并不好用，h 渲染函数来处理 DOM 嵌套的数据又显得非常笨重复杂，此时想要处理一些复杂嵌套 DOM 的问题，就需要使用 slot-scope 作用域插槽来处理了 DOM 嵌套比较复杂的问题。

# 1.函数式组件：

_简单的来说，函数式组件就是一个对象，这个对象有一个 functional 属性为 true，一个 render 属性对应一个函数，这个函数的第一个参数就是渲染函数 h,第二参数是整个组件的上下文 context，里面包含很多这个组件声明的一些信息，还有一个 props 属性用于接收外界传给这个组件的数据。_

```js
let cusSlot = {
    functional: true,
    // Props 是可选的
    props: { // ... },
    // 为了弥补缺少的实例 // 提供第二个参数作为上下文
    render: function (h,context) {
    // ... 可以在这里结合JSX语法，渲染一个简单复用得DOM组件 。当然也可以使用渲染函数h渲染一个合适的虚拟Dom组件。
    }
}
```

# 2.渲染函数 h（也是 createElement）

_这个渲染函数返回的是一个虚拟的 VNode，然后渲染挂载到浏览器上生成我们希望得到的 DOM。_

- ##### 其实 VNode 是 Vuejs 中的一个类 VNode 创建出来的虚拟 DOM 节点，其实就是包含了这个这个虚拟 DOM 节点的所有的信息和属性。对应的就是 DOM 中的节点 Node，本质上是一样的，只不过 Vue 对这个 VNode 做了自己的封装，然后这个节点可以更好的使用在这个框架页面上。（纯属个人理解，可能并不准确)

### h 函数有三个参数：

```js
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // 一个 HTML 标签名、组件选项对象，或者
  // resolve 了上述任何一种的一个 async 函数。必填项。
  "div",

  // {Object}
  // 一个与模板中 attribute 对应的数据对象。可选。
  {
    // (详情见下一节)
  },

  // {String | Array}
  // 子级虚拟节点 (VNodes)，由 `createElement()` 构建而成，
  // 也可以使用字符串来生成“文本虚拟节点”。可选。
  [
    "先写一些文字",
    createElement("h1", "一则头条"),
    createElement(MyComponent, {
      props: {
        someProp: "foobar",
      },
    }),
  ]
);
```

1. 必填 `{String | Object | Function}`   一个 HTML 标签名、组件选项对象，或者是函数返回的一个值。
2. 可选 `{Object}`   一个与模板中 attribute 对应的数据对象,这个对象的属性包含了这个 VNode 的所有的属性、事件和自定义命令等。

```js
{
   class：{String|Object|Array} 和页面组件的class一致。=== :class,
   style:{String|Object|Array}。 === :style
   attrs：{
       ...普通的 HTML attribute
   },
   props:{
       ...组件的prop
   },
   domProps: {
       innerHTML: 'baz'
       ...DOM property
   },
   on:{
       ...VNode点击事件。
   }，
   nativeOn：{
       ...1.仅用于组件，用于监听原生事件，而不是组件内部使用2.`vm.$emit` 触发的事件。
   },
   directives:[
       ...自定义指令
   ],
   scopedSlots: {
       default: props => createElement('span', props.text)
       ...作用域插槽的格式为 { name: props => VNode | Array<VNode> }
   },
   // 如果组件是其它组件的子组件，需为插槽指定名称  
   slot: 'name-of-slot',
   // 其它特殊顶层 property
   key: 'myKey',
   ref: 'myRef',
   // 如果你在渲染函数中给多个元素都应用了相同的 ref 名， // 那么 `$refs.myRef` 会变成一个数组
   refInFor: true
}
```

3. 可选 `{String | Array}` 子级虚拟节点 (VNodes)。因为一个 VNode 中的子级节点可以是一个 VNode，也可以是一段文字，也可以是一个组件渲染而成的 VNode，甚至是一段注释。所以这个参数可以是由 `createElement()` 构建而成的 VNode,也可以使用字符串来生成“文本虚拟节点”。例如：[ `'子级文本数据'`, `createElement('h1', '虚拟 DOM 节点')`, `createElement(MyComponent, { props: { someProp: 'foobar' } })` ]

# 3.slot 插槽

1. ##### 在 Vue 2.6.0 中，我们为具名插槽和作用域插槽引入了一个新的统一的语法 (即 v-slot 指令)。它取代了 slot 和 slot-scope 这两个目前已被废弃但未被移除且仍在文档中的 attribute。因此，在这里只讲 v-slot 指令。
2. ##### 其实插槽顾名思义，就是在设计一个公共组件的时候，留出一个插槽，可以让父组件在使用这个组件的时候在这个插槽中插入任意自己想要插入的内容（比如数据，自定义的原生 DOM，甚至是其他的自定义组件），（当然为了组件在实现的时候避免一些空白的出现,建议在设置插槽的时候，给插槽设置默认内容)

- ### 插槽基本概念：

  _在自己设置的一个公共组件中，设置一个`<slot/>`标签，其中给这个标签设置了 name 属性的是具名插槽例如`<slot name="header"/>`,没设置 name 属性的是不具名插槽，但是相当于是一个名叫 default 的具名插槽。_

  ```html
  <!-- 公用组件base-layout的内部 -->
  <div class="container">
    <header>
      <slot name="header"></slot>
    </header>
    <main>
      <!-- 不具名插槽 等同于<slot name="default"></slot>-->
      <slot></slot>
    </main>
    <footer>
      <slot name="footer"></slot>
    </footer>
  </div>
  ......
  <!-- 使用组件的用法 -->
  <base-layout>
    <template v-slot:header>
      <h1>Here might be a page title</h1>
    </template>
    <!-- <template v-slot:default> -->
    <!-- 没有被v-slot指令包裹的内容，会进入到不具名插槽中，其实这样的写法和v-slot:default是同等效果的 -->
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
    <!-- </template> -->
    <template v-slot:footer>
      <p>Here's some contact info</p>
    </template>
  </base-layout>
  ```

  - 现在 `<template/>`元素中的所有内容都将会被传入相应的插槽。任何没有被包裹在带有 v-slot 的`<template/>` 中的内容都会被视为默认插槽的内容。

- ### 插槽作用域概念：

  - _如果只是可以自定义传递一些固定文字，改变一些样式，那么这个插槽并没有很多用处，所以为了让插槽在组件中发挥更大的作用，就要使用插槽作用域了。（即在父组件使用公共组件的具名插槽的时候，可以获取这个公共组件插槽里的数据）_
  - `因为插槽是带有作用域的，且父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。因此，为了让公共组件中的数据（子级）能在父级组件中被使用，需要在插槽中用v-bind绑定一个你想要使用的数据即可。`

  ```html
  <!-- 公用组件base-layout的内部 -->
  <div class="container">
    <header>
      <slot name="header" :user="user" :dept="dept" :company="company"></slot>
    </header>
    <main>
      <!-- 不具名插槽 等同于<slot name="default"></slot>-->
      <slot :user="user" :dept="dept" :company="company"></slot>
    </main>
    <footer>
      <slot name="footer" :user="user" :dept="dept" :company="company"></slot>
    </footer>
  </div>
  ......
  <!-- 使用组件的用法 -->
  <base-layout>
    <template v-slot:header="{user,dept,company}">
      <h1>{{user.name}}{{dept.title}}{{company.userNumber}}</h1>
      <h1>Here might be a page title</h1>
    </template>
    <template v-slot:default="{user,dept,company}">
      <p>{{user.name}}{{dept.title}}{{company.userNumber}}</p>
      <p>A paragraph for the main content.</p>
      <p>And another one.</p>
    </template>
    <template v-slot:footer="{user,dept,company}">
      <p>{{user.name}}{{dept.title}}{{company.userNumber}}</p>
      <p>Here's some contact info</p>
    </template>
  </base-layout>
  ```

  _还有一些其他用法，可以自行查阅官方文档，这里不多赘述_

  # 4.具体应用

  - tableComponent.vue

```html
<template>
  <div class="tableBox">
    <h3>当前日期：{{Date.now()}}</h3>
    <table width="100%" v-if="tableDatas.length > 0">
      <colgroup>
        <col
          v-for="title in tableTitles"
          :key="title.prop"
          :width="title.width"
        />
      </colgroup>
      <thead>
        <tr v-for="i of titleNum" :key="i + 'theadNum'" height="40px">
          <template v-for="title in tableTitles">
            <template
              v-if="
                parseInt(title[`colspan${i}`]) > 0 &&
                parseInt(title[`rowspan${i}`]) > 0
              "
            >
              <th
                :key="title.prop + 'th' + i"
                :class="title[`thClass${i}`]"
                :colspan="parseInt(title[`colspan${i}`])"
                :rowspan="parseInt(title[`rowspan${i}`])"
                class="th-style"
              >
                <div
                  :class="title[`thDivClass${i}`]"
                  :style="[title[`thDivStyle${i}`]]"
                >
                  {{ title[`label${i}`] }}
                </div>
              </th>
            </template>
            <template
              v-else-if="
                parseInt(title[`colspan${i}`]) > 0 && !title[`rowspan${i}`]
              "
            >
              <th
                :key="title.prop + 'th' + i"
                :class="title[`thClass${i}`]"
                :colspan="parseInt(title[`colspan${i}`])"
                class="th-style"
              >
                <div
                  :class="title[`thDivClass${i}`]"
                  :style="[title[`thDivStyle${i}`]]"
                >
                  {{ title[`label${i}`] }}
                </div>
              </th>
            </template>
            <template
              v-else-if="
                parseInt(title[`rowspan${i}`]) > 0 && !title[`colspan${i}`]
              "
            >
              <th
                :key="title.prop + 'th' + i"
                :class="title[`thClass${i}`]"
                :rowspan="parseInt(title[`rowspan${i}`])"
                class="th-style"
              >
                <div
                  :class="title[`thDivClass${i}`]"
                  :style="[title[`thDivStyle${i}`]]"
                >
                  {{ title[`label${i}`] }}
                </div>
              </th>
            </template>
            <template
              v-else-if="!title[`rowspan${i}`] && !title[`colspan${i}`]"
            >
              <th
                :key="title.prop + 'th' + i"
                :class="title[`thClass${i}`]"
                class="th-style"
              >
                <div
                  :class="title[`thDivClass${i}`]"
                  :style="[title[`thDivStyle${i}`]]"
                >
                  {{ title[`label${i}`] }}
                </div>
              </th>
            </template>
          </template>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(data, index) in tableDatas"
          :key="index + 'td'"
          height="40px"
        >
          <td
            v-for="title in tableTitles"
            :key="title.prop + 'tbodyTd'"
            :class="title.tdClass"
          >
            <template v-if="title.render">
              <cusSlot
                :column="title"
                :row="data"
                :index="index"
                :render="title.render"
              />
            </template>
            <template v-else>
              <slot :name="title.prop" :row="data">
                <div
                  :class="title.tdDivClass"
                  :style="[data[`${title.prop}Style`]]"
                >
                  {{ data[title.prop] }}
                </div>
              </slot>
            </template>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
  /**
   * tableTitles: 表头 label1:代表第一层级的表头名;
   *                   prop:代表该表头对应的这一列的取值的属性值。
   *                   width:表示这个表头的宽度。
   *                   colspan1: 代表第一层的表头的列合并数
   *                   rowspan2：代表第二层的表头的行合并数
   * tableDatas: 表体 表格数据。
   */
  //自定义函数式组件 ==START
  let cusSlot = {
    functional: true, //代表这是一个函数式组件
    props: {
      column: Object, //接受父组件传来得数据
      row: Object,
      index: Number,
      /*接受父组件传来的回调函数，用于把JSX编写的DOM以及DOM上的事件抽取出来到数据脚本页面，
    尽量不污染外部组件，使组件更加的灵活轻便可复用。*/
      render: Function,
    },
    render: (h, context) => {
      //函数式组件用来渲染相应的DOM，有两个参数，h：render渲染函数，context：上下文对象，可以获取相应父组件传到props中的数据
      let cell = {
        column: context.props.column,
        row: context.props.row,
        index: context.props.index,
      };
      //使用父组件传来的render回调函数，将JSX编写的DOM对象抽取出去，保持组件的灵活性。*****关键*****
      /*因为Vue的render函数和JSX的结合不太好，
    所以我们一般只在渲染一些比较简单的DOM时,才使用JSX;
    而是当要渲染一个比较复杂功能的DOM时，一般使用createElement() 函数渲染一个虚拟DOM;
    如果要渲染DOM不仅功能复杂而且结构也很复杂时，建议使用slot 具名插槽来引入相应的DOM。*/
      return context.props.render(h, cell);
    },
  };
  //自定义函数式组件 ==END
  export default {
    name: "tableBox",
    components: {
      cusSlot,
    },
    props: {
      tableTitles: {
        type: Array,
        default: () => [],
      },
      tableDatas: {
        type: Array,
        default: () => [],
      },
      titleNum: {
        type: Number,
        default: 1,
      },
    },
    methods: {},
  };
</script>

<style lang="less">
  .tableBox {
    width: 100%;
    table {
      border-collapse: collapse;
      border-spacing: 0px; //去除表格一些默认样式
      thead tr th {
        padding: 7px 20px;
        box-sizing: border-box;
      }
      tbody tr td {
        padding: 6px 20px;
        box-sizing: border-box;
      }
      thead {
        border-top: 1px solid #000;
        border-bottom: 1px solid #000;
        tr {
          th {
            position: relative;
            &.th-style::before {
              display: block;
              content: "";
              height: 30%;
              width: 1px;
              position: absolute;
              background-color: #000;
              left: 0;
              top: 50%;
              transform: translateY(-50%);
            }
            &.th-style:first-child::before {
              display: none;
            }
            div {
              font-size: 16px;
              line-height: 1.5;
              text-align: left;
            }
          }
        }
      }
      tbody {
        border-bottom: 1px solid #000;
        tr {
          border-bottom: 1px solid #ccc;
          &:last-child {
            border-bottom: unset !important;
          }
          td {
            div {
              font-size: 16px;
              line-height: 1.5;
              text-align: left;
            }
          }
        }
      }
    }
  }
</style>
```

- tableView.vue

```html
<template>
  <div class="home">
    <h1>原生表格组件</h1>
    <tableComponent :tableTitles="tableTitle" :tableDatas="tableDatas">
      <template v-slot:projectType="{ row }">
        <span style="color: red">{{ row.projectType }}</span>
      </template>
      <template v-slot:developmentType="{ row }">
        <span style="color: blue">{{ row.developmentType }}</span>
      </template>
      <template v-slot:lifeCycleType="{ row }">
        <template v-if="row.softType === '非嵌入'">
          <div
            style="
              height: 100%;
              color: green;
              border: 1px solid #000;
              border-radius: 1em;
              padding-left: 4px;
              box-size: border-box;
            "
          >
            {{ `${row.lifeCycleType} MVP` }}
          </div>
        </template>
        <template v-else>
          <div
            style="
              height: 100%;
              color: blue;
              border: 1px solid gold;
              border-radius: 1em;
              padding-left: 4px;
            "
          >
            {{ `${row.lifeCycleType} FMVP` }}
          </div>
        </template>
      </template>
    </tableComponent>
  </div>
</template>

<script>
  // @ is an alias to /src
  import { tableViewDataMixin } from "./tableViewData/tableViewDataMixin";
  export default {
    name: "HomeView",
    mixins: [tableViewDataMixin],
  };
</script>
```

- tableViewDataMixin.js

```js
export const tableViewDataMixin = {
  data() {
    return {
      tableTitle: [
        {
          label1: "序号", // 表头名称
          width: "100px", //表头宽度
          prop: "SN", //表头属性
        },
        {
          label1: "项目类别",
          prop: "projectType",
          width: "200px",
        },
        {
          label1: "软件类型",
          prop: "softType",
          width: "150px",
        },
        {
          label1: "研制类型",
          prop: "developmentType",
          width: "150px",
        },
        {
          label1: "生命周期类型",
          prop: "lifeCycleType",
          width: "200px",
        },
        {
          label1: "更改规模是否必填",
          prop: "changeIsRequired",
          width: "200px",
        },
        {
          label1: "更改规模是否显示",
          prop: "changeIsShow",
          width: "200px",
        },
        {
          label1: "子项目包含类型",
          prop: "subprojectContainsType",
          width: "200px",
        },
        {
          label1: "是否必须包含配置项",
          prop: "isMustIncludeConfigurationItems",
          width: "200px",
        },
        {
          label1: "是否包含开发计划",
          prop: "isIncludeDevelopmentPlan",
          width: "200px",
        },
        {
          label1: "操作",
          prop: "operation",
          width: "",
          render: (h, cell) => {
            return h("button", {
              style: {
                color: "red",
                fontSize: "14px",
                "background-color": "aquamarine",
                "border-radius": "1em",
                border: "1px solid #000",
              },
              on: {
                click: () => this.deleteClick(cell),
              },
              domProps: {
                innerText: "删除",
              },
            });
            /*这里也可以用JSX编写的DOM数据，(这个和上面的render函数渲染出来的虚拟DOM是同等的效果)
            这样就把本来应该写在html的DOM抽出来到JS中，
            然后也可以对这个自定义组件进行处理。这样exSlot这个组件可以进行最大程度的重用。可以保持html的相对简洁。
            但是这两种方法都有缺点，JSX在Vue2.0中并不成熟做不了复杂的DOM操作，render函数渲染虚拟DOM无法进行复杂的DOM嵌套操作，
            所以都不实用，如果遇到复杂的DOM嵌套和复杂的事件操作时，就需要借助v-slot来实现了。*/
            /* return (<button 
                style="color: red;fontSize: "14px";background-color: aquamarine;
                border-radius: 1em;border: 1px solid #000;" 
                onClick = {()=>this.deleteClick(cell)}>
                删除
                </button>)
             */
          },
        },
      ],
      tableDatas: [
        {
          SN: 1,
          projectType: "配置项顶级软件",
          softType: "非嵌入",
          developmentType: "一类",
          lifeCycleType: "一类模型",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
        {
          SN: 2,
          projectType: "配置项顶级软件",
          softType: "非嵌入",
          developmentType: "二类",
          lifeCycleType: "二类模型",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
        {
          SN: 3,
          projectType: "配置项顶级软件",
          softType: "非嵌入",
          developmentType: "三类",
          lifeCycleType: "三类模型",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
        {
          SN: 4,
          projectType: "配置项顶级软件",
          softType: "非嵌入",
          developmentType: "四类",
          lifeCycleType: "四类瀑布",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
        {
          SN: 5,
          projectType: "配置项顶级软件",
          softType: "非嵌入",
          developmentType: "四类",
          lifeCycleType: "四类迭代",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
        {
          SN: 6,
          projectType: "配置项顶级软件",
          softType: "应用软件",
          developmentType: "一类",
          lifeCycleType: "一类模型",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
        {
          SN: 7,
          projectType: "配置项顶级软件",
          softType: "应用软件",
          developmentType: "二类",
          lifeCycleType: "二类模型",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
        {
          SN: 8,
          projectType: "配置项顶级软件",
          softType: "应用软件",
          developmentType: "三类",
          lifeCycleType: "三类模型",
          changeIsRequired: "否",
          changeIsShow: "否",
          subprojectContainsType: "是",
          isMustIncludeConfigurationItems: "否",
          isIncludeDevelopmentPlan: "否",
        },
      ],
    };
  },
  methods: {
    deleteClick(cell) {
      console.log("deleteClick", cell);
    },
  },
};
```
