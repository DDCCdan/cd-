<!--
 * @Date: 2022-10-16 15:24:53
 * @LastEditors: chend25
 * @LastEditTime: 2022-10-16 15:47:19
 * @FilePath: \cd-knowledge-base\notes\javascript.md
-->
## Web Worker
让前端拥有后端的计算能力: 在HTML5的新规范中，实现了 Web Worker 来引入 js 的 多线程 技术, 可以让我们在页面主运行的js线程中，加载运行另外单独的一个或者多个 js线程  
**浏览器有GUI渲染线程与JS引擎线程，这两个线程是互斥的关系**.当js有大量计算时，会造成UI 阻塞，出现界面卡顿、掉帧等情况，严重时会出现页面卡死的情况，俗称假死

## JSBridge
### 作用
通过JSBridge可以实现 H5 和 原生（Native）之间的双向通信，主要是给 H5 提供调用 原生（Native）功能的接口，让混合开发(如Hybrid)中的 H5 可以方便地使用地址位置、摄像头甚至支付等原生功能
### JSBridge 的通信原理
1. 注入API： 注入 API 方式是最常用的方式，主要原理是通过 WebView 提供的接口，向 JavaScript 的 Context（window）中注入对象或者方法，让 JavaScript 调用时，直接执行相应的 Native 代码逻辑，达到 JavaScript 调用 Native 的目的。
2. 拦截 URL SCHEME: Web 端通过某种方式（例如 iframe.src）发送 URL Scheme 请求，之后 Native 拦截到请求，并根据 URL SCHEME（包括所带的参数）进行相关操作（类似JSONP的方式）
   >URL SCHEME：是一种类似于url的链接，是为了方便app直接互相调用设计的，形式和普通的 url 近似，主要区别是 protocol 和 host 一般是自定义的。例如打开微信扫码的SCHEME：weixin://scanqrcod
