<!--
 * @Date: 2022-10-16 20:04:57
 * @LastEditors: chend25
 * @LastEditTime: 2022-10-16 21:29:55
 * @FilePath: \cd-knowledge-base\notes\vue.md
-->
## vuex原理
>参考链接<https://juejin.cn/post/7146996646394462239>

1. vuex中的store本质就是一个没有template模板的隐藏式的vue组件
2. vuex是利用vue的mixin混入机制，在beforeCreate钩子前混入vuexInit方法
3. vuexInit方法实现将vuex store注册到当前组件的$store属性上
4. vuex的state作为一个隐藏的vue组件的data，定义在state上面的变量，相当于这个vue实例的data属性，凡是定义在data上的数据都是响应式的
5. 当页面中使用了vuex state中的数据，就是依赖收集的过程，当vuex中state中的数据发生变化，就通过调用对应属性的dep对象的notify方法，去修改视图变化

## vue 和 react 的区别
1. 数据流的不同：vue支持双向绑定；react不支持双向绑定，需要手动支持，提倡单向数据流（单向数据流与双向绑定不是一组对应的概念）
2. vue组合不同功能的方式是通过mixins，react组合不同功能的方式是通过HoC(高阶组件)
3. 组件通信的区别：Vue中子组件向父组件传递消息有两种方式：事件和回调函数，但Vue更倾向于使用事件。在React中我们都是使用回调函数的
4. 模板渲染方式的不同：React是通过JSX渲染模板。Vue是通过一种拓展的HTML语法进行渲染
5. 渲染过程不同：
   1. 当组件的状态发生变化时，vue 不需要重新渲染整个组件树，通过对应的watcher自动找到对应的组件重新渲染
   2. React在应用的状态被改变时，全部子组件都会重新渲染。通过shouldComponentUpdate这个生命周期方法可以进行控制
6. Vuex和Redux的区别
   1. vuex：vue的状态管理。Vuex数据可变，所以可以直接修改数据
   2. Redux: js的状态管理，react使用redux进行状态管理。Redux提倡数据不可变，用新state替换旧state

## hooks
>参考链接<https://juejin.cn/post/7066951709678895141>

"hooks" 直译是 “钩子”，它并不仅是 react，甚至不仅是前端界的专用术语，而是整个行业所熟知的用语。通常指：
>系统运行到某一时期时，会调用被注册到该时机的回调函数。

比较常见的钩子有：windows 系统的钩子能监听到系统的各种事件，浏览器提供的 onload 或 addEventListener 能注册在浏览器各种时机被调用的方法。  

### react hooks
在 react 中，hooks 是：一系列以 “use” 作为开头的方法，它们提供了让你可以完全避开 class式写法，在函数式组件中完成生命周期、状态管理、逻辑复用等几乎全部组件开发工作的能力。

即 **一系列方法，提供了在函数式组件中完成开发工作的能力。**
```js
import { useState, useEffect, useCallback } from 'react';
// 比如以上这几个方法，就是最为典型的 Hooks
```

#### vue hooks(对比vue2 mixins)
在 vue 中， hooks 的定义可能更模糊: 在 vue **组合式API**里，以 “use” 作为开头的，一系列提供了**组件复用**、**状态管理**等开发能力的方法
```js
import { useSlots, useAttrs } from 'vue';
import { useRouter } from 'vue-router';
// 以上这些方法，也是 vue3 中相关的 Hook！

// 举例 vue 的 Hooks 写法依赖于 组合式API
<template>
  <div>
    {{ message }}
  </div>
</template>
<script setup>
import { computed, ref } from 'vue'
// 定义了一个 ref 对象
const name = ref('')
// 定义了一个依赖 name.value 的计算属性
const message = computed(() => {
  return `hello, my name is ${name.value}`
})
</script>
```

### 为什么vue和react都选择了Hooks
1. **更好的状态复用**： 对于vue2来说，使用的是mixin进行混入，会造成**方法与属性的难以追溯**。随着项目的复杂，文件的增多，经常会出现**不知道某个变量在哪里引入**的，几个文件间来回翻找，同时还会出现**同名变量相互覆盖**的情况
2. **更好的代码组织**： vue2的属性是放到data中，方法定义在methods中，修改某一块的业务逻辑时，经常会出现代码间来回跳转的情况，增加开发人员的负担。hooks代码高度聚合，将“分散在各种声明周期里的代码块”，通过 Hooks 的方式将相关的内容聚合到一起，可读性增加（vue组合式API优势）。
3. 告别this

## vue SSR 服务端渲染
>参考链接<https://juejin.cn/post/7146996646394462239#heading-27>
vue项目，可以使用Nestjs框架，实现ssr渲染，开发有**SEO需求**的页面，解决**首屏时间长**的问题
### SSR基本交互流程
1. 在浏览器访问首页时，Web 服务器根据路由拿到对应数据渲染并输出html，输出的html包含两部分
   1. 路由页对应的页面及已渲染好的数据（后端渲染）
   2. 完整的SPA程序代码
2. 在客户端首屏渲染完成之后，其实已经是一个和之前的 SPA 相差无几的应用程序了，接下来我们进行的任何操作都只是客户端的应用进行交互

### vue SSR整体流程
1. 配置两个入口文件，一个是客户端使用，一个是服务端使用，一套代码两套执行环境
2. 服务端渲染需要Vue实例，每一次客户端请求页面，服务端渲染都是用一个新的Vue实例，服务端利用工厂函数每次都返回一个新的Vue实例
3. 获取请求页面的路由，生成对应的vue实例
4. 如果页面中需要调接口获取数据，通过**asyncData**获取数据，数据获取成功后，通过异步的方式再继续进行初始化，通过vue-server-renderer将数据渲染到页面中，生成html内容

### 为什么服务端渲染不能调用mounted钩子
服务端渲染不能调用beforeMount和mounted，Node环境没有document对象，初始化的时候，vue底层会判断当前环境中是否有el这个dom对象，如果没有，就不会执行到beforeMount和mounted这两个钩子函数