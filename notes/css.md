<!--
 * @Date: 2022-10-16 15:52:50
 * @LastEditors: chend25
 * @LastEditTime: 2023-02-12 22:45:56
 * @FilePath: \cd-knowledge-base\notes\css.md
-->
## BEM命名规范
>参考链接：<https://juejin.cn/post/7096779382740107300>

BEM代表 块（block）、元素（element）、修饰符（modifier），三个部分结合使用，生成一套具有唯一性的class命名规范，起到样式隔离，避免css样式污染的作用。  
如el-input , el-input__inner, el-input--mini
```js
// 样式名前缀、元素、修饰符、状态前缀
$namespace: 'el'; // 所有的组件以el开头，如el-input
$element-separator: '__'; // 元素以__分割，如el-input__inner
$modifier-separator: '--'; // 修饰符以--分割，如el-input--mini
$state-prefix: 'is-'; // 状态以is-开头，如is-disabled
```

## rem + vw 布局
rem+vw布局是手机端常见的布局方案
### rem 布局
本质是等比缩放，rem作用于根元素字体大小。假设屏幕宽度为750px，将屏幕平分为10份，1rem=75px，根元素的fontSize大小为75px
#### rem布局缺点
字体并不合适使用rem, 字体的大小和字体宽度，并不成线性关系，会出现随着屏幕的变大，字体变的越来越大，所以需要结合媒体查询来调整字体大小

### rem + vw 布局
#### 优势
1. 使用纯css的方式来实现，避免使用js动态计算html根元素font-size大小
2. 结合使用媒体查询，解决宽屏下（如ipad）字体过大的问题

## link style @import及三者的区别
1. 加载顺序：
   1. 当一个页面被加载的时候，link引用的CSS会同时被加载
   2. 而@import引用的CSS会等到页面全部被下载完再被加载
2. 加载内容：
   1. @import只能导入样式文件
   2. link不仅可以引入样式，还可以引入js文件
   3. style标签，它是定义在当前页面的样式

## CSS3 硬件加速
CSS3 硬件加速又叫做 GPU 加速，是利用 GPU 进行渲染，减少 CPU 操作的一种优化方案，可以提升网页的性能
### 开启GPU硬件加速的属性有：
1. transform不为none
2. opacity
3. filter
4. will-change

### 弊端
1. GPU处理过多的内容会导致内存问题；
2. 不在动画结束的时候关闭硬件加速，**会出现字体模糊**，GPU渲染会影响字体的抗锯齿效果

## sticky 粘性布局
当元素在屏幕内，表现为relative，当就要滚出屏幕的时候，表现为fixed  
随着页面的滚动，将元素固定在设置的位置（固定效果如同fixed），position:sticky可以看作是position:relative和position:fixed的结合体
### sticky 失效
1. 父元素设置overflow：hidden
2. 父元素高度不够或者高度为内部元素高度之和（总之没有剩余的高度，不会产生滚动）

## flex-basis
>参考链接<https://www.cnblogs.com/eastsae/p/15124416.html>

flex-basis指定了 flex 元素在主轴方向上的初始大小，包含了几种情况：
1. flex-basis有值的情况下，具体是最大值还是最小值，根据flex-grow和 flex-shrink的值来定的。
2. 某一个元素的flex-basis值跟其他兄弟元素相比更大或更小时，表现在缩放上，他们的最终大小也是等于他们的相差数。
3. 当一个元素同时设置了width和flex-basis，此时flex-basis的优先级更高，以flex-basis为准。

## box-sizing
用来控制元素的盒子模型的解析模式，默认为content-box
* context-box：W3C的标准盒子模型（内容content+填充padding+边框border+边界margin），设置元素的 height/width 属性指的是 content 部分的高/宽
* border-box：IE 传统盒子模型（内容（content+padding+border）+ 边界margin）。设置元素的 height/width 属性指的是 content + border + padding 部分的高/宽

## BFC 和 IFC
常见的Formatting Context有 **BFC块级格式上下文** 和 **IFC行内格式上下文**
### BFC布局规则
1. 内部的Box会在垂直方向，一个接一个地放置。
2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
3. 每个元素的左外边缘（margin-left)， 与包含块的左边（contain box left）相接触(对于从左往右的格式化，否则相反)。
4. BFC就是页面上的一个隔离的**独立容器**，容器里面的子元素不会影响到外面的元素。反之也如此。
5. 计算BFC的高度时，浮动元素也参与计算

### BFC形成
1. 浮动 (元素的 float 不是 none)
2. 绝对定位的元素 (元素具有 position 为 absolute 或 fixed)
3. 非块级元素具有 display: inline-block，table-cell, table-caption, flex, inline-flex
4. 块级元素具有overflow ，且值不是 visible
5. 根元素，即 html

### BFC作用
1. 清除浮动
2. 防止垂直margin合并

### IFC布局规则
框(boxes)一个接一个地水平排列，起点是包含块的顶部。水平方向上的 margin，border 和 padding在框之间得到保留

## display的block、inline和inline-block的区别
>参考链接<https://juejin.cn/post/6905539198107942919>
* block： 会独占一行，多个元素会另起一行，可以设置width、height、margin和padding属性；
* inline： 元素不会独占一行，设置width、height属性无效。但可以设置水平方向的margin和padding属性，不能设置垂直方向的padding和margin；
* inline-block： 将对象设置为inline对象，但对象的内容作为block对象呈现，之后的内联对象会被排列在同一行内。

对于行内元素和块级元素，其特点如下：
* 行内元素
   1. 设置宽高无效；
   2. 可以设置水平方向的margin和padding属性，不能设置垂直方向的padding和margin；
   3. 不会自动换行；
* 块级元素
   1. 可以设置宽高；
   2. 设置margin和padding都有效；
   3. 可以自动换行；
   4. 多个块状，默认排列从上到下。

## 伪元素和伪类的区别和作用
### 伪元素
**在内容元素的前后插入额外的元素或样式**，但是这些元素实际上并不在文档中生成。它们只在外部显示可见，但不会在文档的源代码中找到它们，因此，称为“伪”元素
```css
p::before {content:"第一章：";}
p::after {content:"Hot!";}
p::first-line {background:red;}
p::first-letter {font-size:30px;}
```
将每个段落中的第一行字母转换成大写
`p::first-line { text-transform: uppercase }`

### 伪类
将特殊的效果添加到特定选择器上。它是已有元素上添加类别的，**不会产生新的元素**。
```css
a:hover {color: #FF00FF}
p:first-child {color: red}
```

## requestAnimationframe
HTML5 提供一个专门用于请求动画的API——requestAnimationFrame，即 请求动画帧
MDN对该方法的描述：
>window.requestAnimationFrame() 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

取消动画： 使用cancelAnimationFrame()来取消执行动画  

### 优势
1. CPU节能：使用SetTinterval 实现的动画，当页面被隐藏或最小化时，SetTinterval 仍然在后台执行动画任务。使用 RequestAnimationFrame ，当页面处理未激活的状态下，该页面的屏幕刷新任务也会被系统暂停，因此跟着系统走的RequestAnimationFrame也会停止渲染，当页面被激活时，动画就从上次停留的地方继续执行，有效节省了CPU开销
2. 函数节流： RequestAnimationFrame可保证每个刷新间隔内，函数只被执行一次，这样既能保证流畅性，也能更好的节省函数执行的开销。一个刷新间隔内函数执行多次时没有意义的，因为多数显示器每16.7ms刷新一次，多次绘制并不会在屏幕上体现出来
3. 减少DOM操作：requestAnimationFrame 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒60帧

### setTimeout执行动画的缺点
setTimeout通过设定间隔时间来不断改变图像位置，达到动画效果。但是容易出现卡顿、抖动的现象；原因是：
1. settimeout任务被放入异步队列，只有当主线程任务执行完后才会执行队列中的任务，因此实际执行时间总是比设定时间要晚；
2. settimeout的固定时间间隔不一定与屏幕刷新间隔时间相同，会引起丢帧