####2020/6/1
##前端路由
为了构建 SPA（单页面应用），需要引入前端路由系统，这也就是 Vue-Router 存在的意义。前端路由的核心，就在于 —— 改变视图的同时不会向后端发出请求。  
两种模式：hash和history  
hash 模式和 history 模式都属于浏览器自身的属性
####hash模式
url后面带#号，如http://www.abc.com/#/hello  
路由的哈希模式其实是利用了window可以监听onhashchange事件  
通过window.location.hash 获取地址上的hash值  
改变#后面的内容不会引起页面重新刷新，但是会有历史记录
####history模式
url后面不带#号  
需配置mode: 'history'  
利用了 HTML5 History Interface 中新增的 pushState() 和 replaceState() 方法。只是当它们执行修改时，虽然改变了当前的 URL，但浏览器不会立即向后端发送请求。  
history模式不怕前进不怕后退怕刷新，刷新会发送请求，当后端没有配置对应的请求路径资源时，会出现404，为了应对这种情况，服务端可将不存在路径设置重定向到入口文件（index.html）
####区别
hash模式有#，不好看；history模式无#，好看  
回车刷新时：hash模式可以加载到hash值对应页面，history模式可能会出现404情况（history模式的劣势）  
浏览器支持：hash模式支持低版本浏览器和IE浏览器；history是HTML5新推出的API
####参考链接
https://blog.csdn.net/fifteen718/article/details/82529433  
https://juejin.im/post/5b4ca076f265da0f900e0a7d  
https://juejin.im/post/5b31a4f76fb9a00e90018cee

##post和get的区别
* GET参数通过URL传递，POST放在Request body中  
* GET在浏览器回退时是无害的，而POST会再次提交请求  
* GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息  
* GET请求在URL中传送的参数是有长度限制的，而POST没有
* GET请求只能进行url编码，而POST支持多种编码方式
* GET产生的URL地址可以被Bookmark，而POST不可以  
* GET请求会被浏览器主动cache，而POST不会，除非手动设置
####参考链接
https://www.cnblogs.com/logsharing/p/8448446.html

##vue组件通信
####父子组件通信
* 父组件传值给子组件：props  
* 子组件传值给父组件：通过事件形式,this.$emit
* $parent / $children与 ref
####兄弟组件通信
event bus：$on,$emit,$off(销毁)
通过一个空的Vue实例作为中央事件总线（事件中心），用它来触发事件和监听事件,巧妙而轻量地实现了任何组件间的通信，包括父子、兄弟、跨级
####$attrs/$listeners
多级组件嵌套需要传递数据时，通常使用的方法是通过vuex。但如果仅仅是传递数据，而不做中间处理，使用 vuex 处理，未免有点大材小用。为此Vue2.4 版本提供了另一种方法----$attrs/$listeners  
$attrs：包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件。通常配合 inheritAttrs 选项一起使用。
####provide/inject
Vue2.2.0新增API,这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效  
祖先组件中通过provider来提供变量，然后在子孙组件中通过inject来注入变量  
它的使用场景，主要是子组件获取上级组件的状态，跨级组件间建立了一种主动提供与依赖注入的关系  
provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的  

####参考链接
https://juejin.im/post/5cde0b43f265da03867e78d3  

####2020/6/2
##vue中axios封装
在vue项目中，和后台交互获取数据这块，通常使用的是axios库，它是基于promise的http库，可运行在浏览器端和node.js中  
优点：拦截请求和响应、取消请求、转换json、客户端防御XSRF等  

* 设置请求超时： timeout  
* 请求拦截： axios.interceptors.request.use  
* 响应拦截： axios.interceptors.response.use  
* 封装get方法和post方法：post需要设置请求头，默认：  
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8';

####参考链接
https://juejin.im/post/5b55c118f265da0f6f1aa354
##克隆/拷贝
深拷贝和浅拷贝与原数据并不指向同一对象
####浅拷贝
浅拷贝之所以被称为浅拷贝，是因为对象只会被拷贝最外部的一层,至于更深层的对象,则依然是通过引用指向同一块堆内存  
浅拷贝只复制一层对象的属性，并不包括对象里面的为引用类型的数据  
Object.assign(): 一层深克隆，二层浅克隆
####深拷贝
在拷贝引用类型成员变量时，为引用类型的数据成员另辟了一个独立的内存空间，实现真正内容上的拷贝  
深拷贝是对对象以及对象的所有子对象进行拷贝  
JSON.parse：可实现深拷贝，缺点  

* 无法复制函数
* 无法复制正则对象
* 构造函数指向错误：会抛弃对象的constructor,所有的构造函数会指向Object

####参考链接
https://juejin.im/post/5abb55ee6fb9a028e33b7e0a  
https://juejin.im/post/59ac1c4ef265da248e75892b#heading-11
##SPA（单页面应用）和MPA（多页面应用）
####SPA
第一次进入页面时会请求一个html文件，刷新清除一下，切换到其他组件，此时路径也相应变化，但是并没有新的html文件请求，页面内容却变化了  

* 页面跳转： js渲染
* 优点： 页面切换快：页面每次切换跳转时，不需要处理html文件的请求，节约了HTTP发送时延
* 缺点： 
    - 首屏时间稍慢：首屏时需要请求一次html，同时还要发送一次js请求
    - SEO差：搜索引擎只认识html里的内容，不认识js渲染生成的内容，搜索引擎不识别，也就不会给一个好排名
使用vue：服务端渲染技术（SSR）可解决缺点

####MPA
每一次页面跳转的时候，后台服务器都会返回一个新的html文档，这种类型的网站也就是多页网站，也叫多页应用  

* 页面跳转： 返回HTML
* 优点： 
    - 首屏时间快：访问页面的时候，服务器返回一个html，只经历了一个HTTP请求
    - SEO效果好：搜索引擎是可以识别html内容的，每个页面所有的内容都放在html中，所以SEO排名效果好
* 缺点： 切换慢：每次跳转都需要发送一个HTTP请求，如果网络状态不好，在页面间来回跳转时，就会发生明显的卡顿，影响用户体验

####参考链接
https://www.jianshu.com/p/a02eb15d2d70
##vue三要素
模板引擎、响应式、渲染  
Vue 实现流程：  

####1、把模板解析为 render 函数：  
- Vue模板：
    + 本质是字符串；
    + 有逻辑如 v-if v-for 等，模板转换成 js后（render 函数 ）通过js来实现逻辑；
    + 与 html 格式很像，但有很大区别，html 是静态的， Vue 的模板是动态的；
    + 最终 Vue 的模板都要通过 js 转换为 html 来显示  
- vue中如何解析模板：
    + 第一步是将模板通过 parse 函数解析成 AST（抽象语法树）
    + 第二步优化AST（检测出不需要更改的DOM的纯静态子树）
    + 第三步根据优化后的抽象语法树生成包含渲染函数字符串的对象
模板中的所有内容都包含在了 render 函数中

####2、响应式开始监听：
- Object.defineProperty(双向数据绑定)：缺点 【为什么Vue3.x 升级使用 Proxy 取代 Object.defineProperty】
    + Object.defineProperty无法监控到数组下标的变化，导致通过数组下标添加元素，不能实时响应(可用$set添加）；
     + Object.defineProperty只能劫持对象的属性，从而需要对每个对象，每个属性进行遍历，如果属性值是对象，还需要深度遍历。Proxy可以劫持整个对象，并返回一个新的对象。
    + Proxy不仅可以代理对象，还可以代理数组。还可以代理动态增加的属性
- 将data的属性代理到vm上

####3、首次渲染，显示页面且绑定依赖
####4、data属性变化，触发render
- 修改属性，被响应式的set监听到
- set中执行 updataComponent （ 异步 ）
- updataComponent重新执行 vm.render()
- 生成的vnode和prev Vnode，通过patch进行比较渲染到html 中

####参考链接
https://blog.csdn.net/weixin_33709364/article/details/88010302  
https://blog.csdn.net/webFrontEndDev/article/details/102702402  
https://juejin.im/post/5e7ae687f265da57424bb691#heading-11

####2020/6/3
##虚拟DOM（virtual DOM）
用 JS 模拟 DOM 结构  

* 操作真实DOM代价昂贵，如：在一次操作中，需要更新10个DOM节点，浏览器收到第一个DOM请求后并不知道还有9次更新操作，因此会马上执行流程，最终执行10次。频繁操作还是会出现页面卡顿，影响用户体验
* 虚拟DOM可提高浏览器性能：若一次操作中有10次更新DOM的动作，虚拟DOM不会立即操作DOM，而是将这10次更新的diff内容保存到本地一个JS对象中，最终将这个JS对象一次性attch到DOM树上，再进行后续操作，避免大量无谓的计算量
* JS对象模拟DOM节点的好处：页面的更新可以先全部反映在JS对象(虚拟DOM)上，操作内存中的JS对象的速度显然要更快，等更新完成后，再将最终的JS对象映射成真实的DOM，交由浏览器去绘制

####虚拟DOM的核心API  
* h函数（将真实dom映射成虚拟节点）  
    h(<标签名>，{属性}，[children])//含有子节点的  
    h(<标签名>，{属性}，'text'])//没有子节点，只有文本，如p标签
* patch函数（通过对比新旧虚拟节点，找出差异（diff算法），把变化更新到真实dom中）  
    patch(container, vnode)//初次渲染  
    patch(oldVnode, newVnode); //re-render

####diff算法 
找出本次DOM必须更新的节点来更新，其它的不更新，这个“找出”的过程，就需要diff算法  
使用diff算法来比较旧VNode及新的VNode之间的差异然后执行Patch Operation或者叫 Patch函数来高效更新 Dom 节点  
实现过程：  

 * patch（container，vnode）：核心逻辑createElment
 * patch（vnode，newVnode）：核心逻辑updataChildren
 
####参考链接  
https://www.cnblogs.com/charliePU/p/10791165.html  
https://www.jianshu.com/p/af0b398602bc

####2020/6/4
##vuex
Vuex是一个专为Vue.js应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化  
把组件的共享状态抽取出来，以一个全局单例模式管理，在这种模式下，组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为  
Vuex和单纯的全局对象的不同：  

* Vuex 的状态存储是响应式的
* 不能直接改变store中的状态，只能通过mutation或者actions（异步）改变store中的状态

####vuex的组成
* state：使用 state来存储应用中需要共享的状态。为了能让 Vue 组件在 state更改后也随着更改，需要基于state创建计算属性（mapGetters，mapState）
* getters
* mutations（同步）：改变store中的状态
* actions（异步）：异步地更改状态，action并不直接改变state，而是发起mutation
* Module: 模块化store。由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store对象就有可能变得相当臃肿。Vuex 允许我们将 store分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块

####工作流程
* 1、数据从state中渲染到页面
* 2、在页面通过dispatch来触发action
* 3、action通过调用commit,来触发mutation
* 4、mutation来更改数据，数据变更之后会触发dep对象的notify，通知所有Watcher对象去修改对应视图（vue的双向数据绑定原理）

####应用场景
* 多个视图依赖于同一状态
* 来自不同视图的行为需要改变同一个状态

####参考链接
https://juejin.im/post/5e7ae687f265da57424bb691#heading-21  
##vue路由（vue-router）
####组件
* `<router-link>`
* `<router-view>`:用于渲染匹配的组件
* `<keep-alive>`:是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染 。也就是所谓的-----组件缓存

####路由分类
* 动态路由:使用 path 属性过程中，使用动态路径参数，以冒号开头,如：  
```
{
    path: /user/:id     //id为1，路径为 /user/1  
    component: User  
}
```
* 嵌套路由:`<router-view>`,需要在 VueRouter 的参数中使用 children 配置
导航：

* 声明式 `<router-link :to="...`
* 编程式 `router.push(...)`

####路由对象
使用了vue-router的应用中,路由对象会被注入每个组件中，赋值为 this.$route  
属性：  

* $route.path 
* $route.params
* $route.query 
* $route.router 
* $route.name 
* $route.matched:数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象,如/home/news/detail/:id这条路径，它包含3条匹配的路由：  
    + /home/news/detail/:id
    + /home/news
    + /home

####路由的钩子函数（导航守卫）
路由的切换过程，本质上是执行一系列路由钩子函数，钩子函数总体上分为两大类：  

* 全局的钩子函数
    + beforeEach：在路由切换开始时调用
    + afterEach：在每次路由切换成功进入激活阶段时被调用
* 单个路由的钩子函数
    + beforeEnter
    + beforeLeave
* 组件的钩子函数
    + beforeRouteEnter ：在渲染该组件的对应路由被 confirm 前调用
    + beforeRouteUpdate：在当前路由改变，但是该组件被复用时调用
    + beforeRouteLeave：导航离开该组件的对应路由时调用

####重定向和别名
重定向：`routes: [{ path: '/a', redirect: '/b' }]`  
别名：`routes: [{ path: '/a', component: A, alias: '/b' }]`
####常见问题
1、route和router的区别？  

* router为VueRouter的实例，是一个全局路由对象，包含了路由跳转的方法、钩子函数等
* route 是路由信息对象||跳转的路由对象，每一个路由都会有一个route对象，是一个局部对象，包含path,params,hash,query,fullPath,matched,name等路由信息参数  

2、vue-router实现路由懒加载（动态加载路由）  

* 把不同路由对应的组件分割成不同的代码块，然后当路由被访问时才加载对应的组件即为路由的懒加载，可以加快项目的加载速度，提高效率  

3、active-class 是哪个组件的属性？  

* active-class是router-link终端属性，用来做选中样式的切换，当router-link标签被点击时将会应用这个样式  

####参考链接
https://www.cnblogs.com/axl234/p/5899952.html  
https://juejin.im/post/5e7ae687f265da57424bb691#heading-24

####2020/6/5
##Mixin（混入）
混入 (mixin) 提供了一种非常灵活的方式，来分发Vue组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项  
####使用场景
有两个非常相似的组件，他们的基本功能是一样的，但他们之间又存在着足够的差异性，此时的你就像是来到了一个分岔路口：我是把它拆分成两个不同的组件呢？还是保留为一个组件，然后通过props传值来创造差异性从而进行区分呢？  
两种解决方案都不够完美：如果拆分成两个组件，你就不得不冒着一旦功能变动就要在两个文件中更新代码的风险，这违背了DRY原则（Don’t Repeat Yourself）。反之，太多的props传值会很快变得混乱不堪，从而迫使维护者在使用组件的时候必须理解一大段的上下文，拖慢写码速度。  
Mixin允许你封装一块在应用的其他组件中都可以使用的**函数**。如果使用姿势得当，他们不会改变函数作用域外部的任何东西，因此哪怕执行多次，只要是同样的输入你总是能得到一样的值  
即页面的风格不用，但是执行的方法和需要的数据类似可以使用mixin

####2020/6/10
##vue自定义指令（项目中实践用到的）
####使用方式
* 注册全局指令--用 __directive__
```
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```
* 注册局部指令--用 __directives__
```
// 注册一个注册局部指令 `v-focus`
data(){
},
directives: {
  focus: {
    // 指令的定义,钩子函数等
    inserted: function (el) {
      el.focus()
    }
  }
}
```
或者
```
const focus = {
    //钩子函数等
}
```
绑定使用
`<input v-focus>`
####指令定义对象的钩子函数
* bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
* inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中
* update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 。
* componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
* unbind：只调用一次，指令与元素解绑时调用。

####钩子函数的参数
* el：指令所绑定的元素，可以用来直接操作 DOM。
* binding：一个对象，包含以下 property：
    - name：指令名，不包括 v- 前缀。
    - value：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2。
    - oldValue：指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
    - expression：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。
    - arg：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
    - modifiers：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。
* vnode：Vue 编译生成的虚拟节点。
* oldVnode：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。
除了 el之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过元素的 dataset 来进行

####使用实例代码
* 实例一 点击除div之外的地方隐藏div的实现（局部）
```
<template>
<!-- 绑定具体关闭div方法 -->
  <div v-clickoutside="handleClose" v-show="show">
  显示
  </div>
</template>
<script>
const clickoutside = {
    // 初始化指令
    bind(el, binding, vnode) {
      function documentHandler(e) {
        // 这里判断点击的元素是否是本身，是本身，则返回
        if (el.contains(e.target)) {
          return false;
        }
        // 判断指令中是否绑定了函数
        if (binding.expression) {
          // 如果绑定了函数 则调用那个函数，此处binding.value就是handleClose方法
          binding.value(e);
        }
      }
      // 给当前元素绑定个私有变量，方便在unbind中可以解除事件监听
      el.__vueClickOutside__ = documentHandler;
      document.addEventListener('click', documentHandler);
    },
    update() {},
    unbind(el, binding) {
      // 解除事件监听
      document.removeEventListener('click', el.__vueClickOutside__);
      delete el.__vueClickOutside__;
    },
  };
  export default {
    name: 'HelloWorld',
    data() {
        return {
            show: true,
        };
    },
    directives: {clickoutside},
    methods: {
        handleClose(e) {
            this.show = false;
        },
    },
};
</script>
```
* 实例二 防重复点击(全局)
```
Vue.directive('noMoreClick', {
  inserted (el, binding) {
    el.addEventListener('click', e => {
      //el.classList.add('is-loading')  //添加类名
      el.disabled = true
      setTimeout(() => {
        el.disabled = false
      }, 1000)
    })
  }
})
```

####参考链接
https://blog.csdn.net/weixin_33877092/article/details/91368472  
https://cn.vuejs.org/v2/guide/custom-directive.html

####2020/6/11
##原型、原型链
JavaScript没有"子类"和"父类"的概念，也没有"类"（class）和"实例"（instance）的区分，全靠一种很奇特的"原型链"（prototype chain）模式，来实现继承
**在Javascript语言中，new命令后面跟的不是类，而是构造函数**
```
function DOG(name){
    this.name = name;
}
var dogA = new DOG('大毛');
alert(dogA.name); // 大毛
```
DOG的构造函数，表示狗对象的原型,对这个构造函数使用new，就会生成一个狗对象的实例
####new运算符的缺点
**用构造函数生成实例对象，那就是无法共享属性和方法**
每一个实例对象，都有自己的属性和方法的副本。这不仅无法做到数据共享，也是极大的资源浪费
####protoType属性
protoType属性包含一个对象（以下简称"prototype对象"），所有实例对象需要共享的属性和方法，都放在这个对象里面；那些不需要共享的属性和方法，就放在构造函数里面  
实例对象一旦创建，将自动引用prototype对象的属性和方法。也就是说，实例对象的属性和方法，分成两种，一种是本地的，另一种是引用的  
由于所有的实例对象共享同一个prototype对象，那么从外界看起来，prototype对象就好像是实例对象的原型，而实例对象则好像"继承"了prototype对象一样
####原型链
js里完全依靠"原型链"(prototype chain)模式来实现继承

* \_\_proto\_\_：事实上就是原型链指针，指向了 **实例对象的原型**，它也是一个对象
* prototype：上面说到这个是 **指向原型对象** 的
* constructor：每一个原型对象都包含一个指向 **构造函数** 的指针，就是constructor

构造函数的 prototype 指向调用该构造函数而创建的实例对象的原型  
```
function Cat() {
    this.color = 'orange'
}
var cat = new Cat()
console.log(cat.__proto__ === Cat.prototype)   // true
```
构造函数和构造函数的 prototype 可以相互指向  
```
function Cat() {
    this.color = 'orange'
}
console.log(Cat.prototype.constructor === Cat)    // true
```
Object.prototype 的原型是null  (Object.prototype 没有原型)  
 `console.log(Object.prototype.__proto__ === null) // true`
####继承
实例.\_\_proto\_\_ === 原型  
原型.constructor === 构造函数  
构造函数.prototype === 原型
####参考链接
http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html  
https://www.yuque.com/fe9/basic/zk5e4f

####2020/6/18
##vue 自定义过滤器（filter）
**注：可联系上文的（自定义指令）**
过滤器可以用在两个地方：**双花括号插值**和 **v-bind表达式**   
```
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```
capitalize 过滤器函数将会收到 message 的值作为第一个参数  
前者为过滤器参数，后者为过滤器函数
####组件过滤器和全局过滤器
当全局过滤器和局部过滤器重名时，会采用局部过滤器

* 组件过滤器————**filters**
```
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```
* 全局过滤器————**filter**
```
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  // ...
})
```

####参考链接
https://cn.vuejs.org/v2/guide/filters.html  
https://www.jianshu.com/p/ad21df1914c5  

####2020/6/22
##CSS布局--dispaly、position、float
p, ul, form, div等元素被称为块级元素，这些元素显示为一块儿内容（会自动换行），span, input 等元素称为行内元素  

* 块级元素会从上到下一个个垂直排列，每个自占一行  
* 行内元素在一行中水平排列，行内元素的高度由其内容撑开，不可显示的设置其高度

####定位--position
* inhert：规定应该从父元素继承 position 属性的值。
* static：默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。
* relative：生成相对定位的元素，相对于元素本身正常位置进行定位。因此，"left:20" 会向元素的 LEFT 位置添加 20 像素。
* absolute：生成绝对定位的元素，相对于 static 定位以外的第一个祖先元素进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。
* fixed：生成绝对定位的元素，相对于浏览器窗口进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。

CSS有三种基本的定位机制：**普通流、浮动和绝对定位**  
绝对定位的元素的位置是相对于距离他最近的非static祖先元素位置决定的  
####浮动
clear:both可以清除浮动  
应用值为hidden或auto的overflow属性会有一个副作用：自动清理包含的任何浮动元素
####参考链接
https://www.cnblogs.com/dolphinX/archive/2012/10/13/2722501.html  
https://blog.csdn.net/liulanzaijia/article/details/87725232

####2020/6/23
##inline-block和float
* **inline-block**： inline-block的元素就是宽高可设置，相邻的元素会在一行显示，直到本行排满，也就是让元素的容器属性为block，内容为inline
* **float**： 设置元素的浮动为左或者右浮动，当设置元素浮动时，相邻元素会根据自身大小，排满一行，如果父容器宽度不够则会换行。当我们设置了元素的浮动时，这个元素就脱离了文档流，相邻元素会呈环绕装排列

####区别
* inline-block: 水平排列一行，即使元素高度不一，也会以高度最大的元素高度为行高，即使高度小的元素周围留空，也不回有第二行元素上浮补位。可以设置默认的垂直对齐基线
* 让元素脱离当前文档流，呈环绕装排列，如遇上行有空白，而当前元素大小可以挤进去，这个元素会在上行补位排列。默认是顶部对齐

当我们要设置某些元素在一行显示，并且排列方向一致的情况下，我们尽可能去用inline-block。因为inline-block的元素仍然在当前文档流里面，这样就减少了程序对DOM的更改操作，因为DOM的每一次更改，浏览器会重绘DOM树。理论上会增加性能消耗。这样也不用像flaot那样麻烦，需要清除flaot  
####参考链接
https://www.cnblogs.com/scot/p/5501669.html

##cookie、session、token（令牌）
####cookie
* **HTTP 是无状态的协议**（对于事务处理没有记忆能力，每次客户端和服务端会话完成时，服务端不会保存任何会话信息）：每个请求都是完全独立的，服务端无法确认当前访问者的身份信息，无法分辨上一次的请求发送者和这一次的发送者是不是同一个人。所以服务器与浏览器为了进行会话跟踪（知道是谁在访问我），就必须主动的去维护一个状态，这个状态用于告知服务端前后两个请求是否来自同一浏览器。而这个状态需要通过 cookie 或者 session 去实现。
* **cookie 存储在客户端**： cookie 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。
* **cookie 是不可跨域的**： 每个 cookie 都会绑定单一的域名，无法在别的域名下获取使用，一级域名和二级域名之间是允许共享使用的（靠的是 domain）

**cookie属性**  

|  属性   | 说明  |
|  ----  | ----  |
| name=value  | 键值对，设置 Cookie 的名称及相对应的值，都必须是字符串类型 |
| domain  | 指定 cookie 所属域名，默认是当前域名 |
| path  | 指定 cookie 在哪个路径（路由）下生效，默认是 '/'。 |
| maxAge  | cookie 失效的时间，单位秒。 比 expires 好用  |
| expires  | 过期时间，在设置的某个时间点后该 cookie 就会失效  |
| secure  | 该 cookie 是否仅被使用安全协议传输。当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效  |
| httpOnly  | 如果给某个 cookie 设置了 httpOnly 属性，则无法通过 JS 脚本 读取到该 cookie 的信息，但还是能通过 Application 中手动修改 cookie，所以只是在一定程度上可以防止 XSS 攻击，不是绝对的安全  |

####session
* session 是另一种记录服务器和客户端会话状态的机制  
* session 是基于 cookie 实现的，session 存储在服务器端，**sessionId** 会被存储到客户端的cookie 中  
* session 认证流程
    + 用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建对应的 Session
    + 请求返回时将此 Session 的唯一标识信息 SessionID 返回给浏览器
    + 浏览器接收到服务器返回的 SessionID 信息后，会将此信息存入到 Cookie 中，同时 Cookie 记录此 SessionID 属于哪个域名
    + 当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作
**SessionID 是连接 Cookie 和 Session 的一道桥梁，大部分系统也是根据此原理来验证用户登录状态**

####cookie与session的区别
* 安全性： Session 比 Cookie 安全，Session 是存储在服务器端的，Cookie 是存储在客户端的。
* 存取值的类型不同：Cookie 只支持存字符串数据，想要设置其他类型的数据，需要将其转换成字符串，Session 可以存任意数据类型。
* 有效期不同： Cookie 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭（默认情况下）或者 Session 超时都会失效。
* 存储大小不同： 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie，但是当访问量过多，会占用过多的服务器资源

简而言之, session 有如用户信息档案表, 里面包含了用户的认证信息和登录状态等信息. 而 cookie 就是用户通行证

####token
token 也称作令牌，由uid+time+sign[+固定参数]  
token 的认证方式类似于临时的证书签名, 并且是一种服务端无状态的认证方式, 非常适合于 REST API 的场景. 所谓无状态就是服务端并不会保存身份认证相关的数据  
**存放**：token在客户端一般存放于localStorage，cookie，或sessionStorage中。在服务器一般存于数据库中
* Acesss Token
    + 访问资源接口（API）时所需要的资源凭证
    + 简单 token 的组成： uid(用户唯一的身份标识)、time(当前时间的时间戳)、sign（签名，token 的前几位以哈希算法压缩成的一定长度的十六进制字符串）
    + 特点：
        - 服务端无状态化、可扩展性好
        - 支持移动端设备
        - 安全
        - **支持跨程序调用**
    + token 的身份验证流程  
        1.客户端使用用户名跟密码请求登录  
        2.服务端收到请求，去验证用户名与密码  
        3.验证成功后，服务端会签发一个token并把这个token发送给客户端  
        4.客户端收到token以后，会把它存储起来，比如放在cookie里或者localStorage里  
        5.客户端每次向服务端请求资源的时候需要带着服务端签发的token服务端收到请求，然后去验证客户端请求里面带着的token，如果验证成功，就向客户端返回请求的数据
    + 每一次请求都需要携带 token，需要把 token 放到HTTP的Header里
    + 基于 token 的用户认证是一种服务端无状态的认证方式，服务端不用存放token数据。用解析token的计算时间换取session的存储空间，从而减轻服务器的压力，减少频繁的查询数据库
    + token 完全由应用管理，所以它可以避开同源策略

**token可以抵抗csrf，cookie+session不行**

####分布式情况下的session和token
session时有状态的，一般存于服务器内存或硬盘中，当服务器采用分布式或集群时，session就会面对负载均衡问题  

* 负载均衡多服务器的情况，不好确认当前用户是否登录，因为多服务器不共享session。这个问题也可以将session存在一个服务器中来解决，但是就不能完全达到负载均衡的效果。

而token是无状态的，token字符串里就保存了所有的用户信息  

* 客户端登陆传递信息给服务端，服务端收到后把用户信息加密（token）传给客户端，客户端将token存放于localStroage等容器中。客户端每次访问都传递token，服务端解密token，就知道这个用户是谁了。通过cpu加解密，服务端就不需要存储session占用存储空间，就很好的解决负载均衡多服务器的问题了。这个方法叫做JWT(Json Web Token)

####JWT（Json Web Token）
* JSON Web Token（简称 JWT）是目前最流行的跨域认证解决方案。
* 是一种认证授权机制。JWT 是为了在网络应用环境间传递声明而执行的一种基于 JSON 的开放标准（RFC 7519）。
* JWT 的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源。比如用在用户登录上。
* 可以使用HMAC算法或者是RSA的公/私秘钥对JWT进行签名。因为数字签名的存在，这些传递的信息是可信的。
* 具体可参考 http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html
**JWT组成**
* Header（头部）：一个JSON对象，描述JWT的元数据，（使用 Base64URL算法将JSON对象转成字符串）
* Payload（负载）：一个JSON对象，用来存放实际需要传递的数据，**JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分**（这个 JSON 对象也要使用 Base64URL 算法转成字符串）
* Signature（签名）：是对前两部分的签名，防止数据篡改
用.分隔，即Header.Payload.Signature
**JWT使用方式**
客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。此后，客户端每次与服务器通信，都要带上这个 JWT。
* 可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息Authorization字段里面`Authorization: Bearer <token>`
* 跨域的时候，JWT就放在POST请求的数据体里面
* 通过 URL 传输 如`http://www.example.com/user?token=xxx`

####总结
* session存储于服务器，可以理解为一个状态列表，拥有一个唯一识别符号sessionId，通常存放于cookie中。服务器收到cookie后解析出sessionId，再去session列表中查找，才能找到相应session。依赖cookie
* cookie类似一个令牌，装有sessionId，存储在客户端，浏览器通常会自动添加。
* token也类似一个令牌，无状态，用户信息都被加密到token中，服务器收到token后解密就可知道是哪个用户。需要开发者手动添加。
* jwt只是一个跨域认证的方案

####参考链接
https://juejin.im/post/5e055d9ef265da33997a42cc  
https://segmentfault.com/a/1190000017831088