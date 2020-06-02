####2020/6/1
##前端路由
为了构建 SPA（单页面应用），需要引入前端路由系统，这也就是 Vue-Router 存在的意义。前端路由的核心，就在于 —— 改变视图的同时不会向后端发出请求。  
两种模式：hash和history  
hash 模式和 history 模式都属于浏览器自身的属性
#####hash模式
url后面带#号，如http://www.abc.com/#/hello  
路由的哈希模式其实是利用了window可以监听onhashchange事件  
通过window.location.hash 获取地址上的hash值  
改变#后面的内容不会引起页面重新刷新，但是会有历史记录
#####history模式
url后面不带#号  
需配置mode: 'history'  
利用了 HTML5 History Interface 中新增的 pushState() 和 replaceState() 方法。只是当它们执行修改时，虽然改变了当前的 URL，但浏览器不会立即向后端发送请求。  
history模式不怕前进不怕后退怕刷新，刷新会发送请求，当后端没有配置对应的请求路径资源时，会出现404，为了应对这种情况，服务端可将不存在路径设置重定向到入口文件（index.html）
#####区别
hash模式有#，不好看；history模式无#，好看  
回车刷新时：hash模式可以加载到hash值对应页面，history模式可能会出现404情况（history模式的劣势）  
浏览器支持：hash模式支持低版本浏览器和IE浏览器；history是HTML5新推出的API
#####参考链接
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
#####参考链接
https://www.cnblogs.com/logsharing/p/8448446.html

##vue组件通信
#####父子组件通信
* 父组件传值给子组件：props  
* 子组件传值给父组件：通过事件形式,this.$emit
* $parent / $children与 ref
#####兄弟组件通信
event bus：$on,$emit,$off(销毁)
通过一个空的Vue实例作为中央事件总线（事件中心），用它来触发事件和监听事件,巧妙而轻量地实现了任何组件间的通信，包括父子、兄弟、跨级
#####$attrs/$listeners
多级组件嵌套需要传递数据时，通常使用的方法是通过vuex。但如果仅仅是传递数据，而不做中间处理，使用 vuex 处理，未免有点大材小用。为此Vue2.4 版本提供了另一种方法----$attrs/$listeners  
$attrs：包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件。通常配合 inheritAttrs 选项一起使用。
#####provide/inject
Vue2.2.0新增API,这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效  
祖先组件中通过provider来提供变量，然后在子孙组件中通过inject来注入变量  
它的使用场景，主要是子组件获取上级组件的状态，跨级组件间建立了一种主动提供与依赖注入的关系  
provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的

#####参考链接
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
#####参考链接
https://juejin.im/post/5b55c118f265da0f6f1aa354
##克隆/拷贝
深拷贝和浅拷贝与原数据并不指向同一对象
#####浅拷贝
浅拷贝之所以被称为浅拷贝，是因为对象只会被拷贝最外部的一层,至于更深层的对象,则依然是通过引用指向同一块堆内存  
浅拷贝只复制一层对象的属性，并不包括对象里面的为引用类型的数据  
Object.assign(): 一层深克隆，二层浅克隆
#####深拷贝
在拷贝引用类型成员变量时，为引用类型的数据成员另辟了一个独立的内存空间，实现真正内容上的拷贝  
深拷贝是对对象以及对象的所有子对象进行拷贝  
JSON.parse：可实现深拷贝，缺点  

* 无法复制函数
* 无法复制正则对象
* 构造函数指向错误：会抛弃对象的constructor,所有的构造函数会指向Object
#####参考链接
https://juejin.im/post/5abb55ee6fb9a028e33b7e0a  
https://juejin.im/post/59ac1c4ef265da248e75892b#heading-11
##SPA（单页面应用）和MPA（多页面应用）
#####SPA
第一次进入页面时会请求一个html文件，刷新清除一下，切换到其他组件，此时路径也相应变化，但是并没有新的html文件请求，页面内容却变化了  

* 页面跳转： js渲染
* 优点： 页面切换快：页面每次切换跳转时，不需要处理html文件的请求，节约了HTTP发送时延
* 缺点： 
    - 首屏时间稍慢：首屏时需要请求一次html，同时还要发送一次js请求
    - SEO差：搜索引擎只认识html里的内容，不认识js渲染生成的内容，搜索引擎不识别，也就不会给一个好排名
使用vue：服务端渲染技术（SSR）可解决缺点
#####MPA
每一次页面跳转的时候，后台服务器都会返回一个新的html文档，这种类型的网站也就是多页网站，也叫多页应用  

* 页面跳转： 返回HTML
* 优点： 
    - 首屏时间快：访问页面的时候，服务器返回一个html，只经历了一个HTTP请求
    - SEO效果好：搜索引擎是可以识别html内容的，每个页面所有的内容都放在html中，所以SEO排名效果好
* 缺点： 切换慢：每次跳转都需要发送一个HTTP请求，如果网络状态不好，在页面间来回跳转时，就会发生明显的卡顿，影响用户体验
#####参考链接
https://www.jianshu.com/p/a02eb15d2d70
##vue三要素
模板引擎、响应式、渲染  
Vue 实现流程：  

#####1、把模板解析为 render 函数：  
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
#####2、响应式开始监听：
- Object.defineProperty(双向数据绑定)：缺点 【为什么Vue3.x 升级使用 Proxy 取代 Object.defineProperty】
    + Object.defineProperty无法监控到数组下标的变化，导致通过数组下标添加元素，不能实时响应(可用$set添加）；
     + Object.defineProperty只能劫持对象的属性，从而需要对每个对象，每个属性进行遍历，如果属性值是对象，还需要深度遍历。Proxy可以劫持整个对象，并返回一个新的对象。
    + Proxy不仅可以代理对象，还可以代理数组。还可以代理动态增加的属性
- 将data的属性代理到vm上
#####3、首次渲染，显示页面且绑定依赖
#####4、data属性变化，触发render
- 修改属性，被响应式的set监听到
- set中执行 updataComponent （ 异步 ）
- updataComponent重新执行 vm.render()
- 生成的vnode和prev Vnode，通过patch进行比较渲染到html 中
#####参考链接
https://blog.csdn.net/weixin_33709364/article/details/88010302  
https://blog.csdn.net/webFrontEndDev/article/details/102702402  
https://juejin.im/post/5e7ae687f265da57424bb691#heading-11
