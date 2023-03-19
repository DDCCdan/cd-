## Javascript
### this
#### 普通函数
1. 一旦确定，不可以被更改  
```js
var a = 10;
var obj = {
  a: 20
}

function fn() {
  this = obj; // 修改this，运行后会报错
  console.log(this.a);
}
```
2. this永远指向的是最后调用它的对象 —— **执行时确定**

```js
var name = 'window'; // 其实是window.name = 'window'
var A = {
  name: 'A',
  sayHello: function(){
    console.log(this.name)
  }
}
A.sayHello();// 输出A
var B = {
  name: 'B'
}
A.sayHello.call(B);//输出B
A.sayHello.call();//不传参数指向全局window对象，输出window.name也就是window
```

#### 箭头函数
1. 箭头函数体内的`this`对象，就是 **定义该函数时所在的作用域指向的对象**，而不是使用时所在的作用域指向的对象

```js
var name = 'window'; 
var A = {
  name: 'A',
  sayHello: () => {
    console.log(this.name)
  }
}
A.sayHello();// 还是以为输出A ? 错啦，其实输出的是window
```
* **“该函数所在的作用域指向的对象”**，作用域是指函数内部，这里的箭头函数，也就是sayHello，所在的作用域其实是最外层的js环境，因为没有其他函数包裹；然后最外层的js环境指向的对象是winodw对象，所以这里的this指向的是window对象。

```js
var name = 'window'; 
var A = {
  name: 'A',
  sayHello: function(){
    var s = () => console.log(this.name)
    return s//返回箭头函数s
  }
}
var sayHello = A.sayHello();
sayHello();// 输出A 
var B = {
  name: 'B';
}
sayHello.call(B); //还是A
sayHello.call(); //还是A
```
根据 **“该函数所在的作用域指向的对象”** 来分析
1. **该函数所在的作用域**：箭头函数s 所在的作用域是sayHello,因为sayHello是一个函数。
2. **作用域指向的对象**：A.sayHello指向的对象是A。
   
#### 箭头函数特点
1. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
2. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
3. 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

-----

**参考链接**
* <https://zhuanlan.zhihu.com/p/57204184>

### typeof 和 instanceof
#### typeof
1. typeof 可用于判断基础数据类型
2. 无法判断引用类型  
   * `typeof null // object`
   * `typeof console.log // function`

#### instanceof
`instanceof` 运算符用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上

#### 区别
1. `typeof` 会返回一个变量的基本类型，`instanceof` 返回的是一个布尔值
2. `instanceof` 可以准确地判断复杂引用数据类型，但是不能正确判断基础数据类型
3. `typeof` 判断基础数据类型（`null` 除外），引用数据类型中，只能判断 `function` 类型
   
#### 通用检测数据类型
可以采用 `Object.prototype.toString`，调用该方法，统一返回格式 `“[object Xxx]”` 的字符串
```js
Object.prototype.toString({})       // "[object Object]"
Object.prototype.toString.call({})  // 同上结果，加上call也ok
Object.prototype.toString.call(1)    // "[object Number]"
Object.prototype.toString.call('1')  // "[object String]"
Object.prototype.toString.call(true)  // "[object Boolean]"
Object.prototype.toString.call(function(){})  // "[object Function]"
Object.prototype.toString.call(null)   //"[object Null]"
Object.prototype.toString.call(undefined) //"[object Undefined]"
Object.prototype.toString.call(/123/g)    //"[object RegExp]"
Object.prototype.toString.call(new Date()) //"[object Date]"
Object.prototype.toString.call([])       //"[object Array]"
Object.prototype.toString.call(document)  //"[object HTMLDocument]"
Object.prototype.toString.call(window)   //"[object Window]"
```
通用数据类型判断方法
```js
function getType(obj){
  let type  = typeof obj;
  if (type !== "object") {    // 先进行typeof判断，如果是基础数据类型，直接返回
    return type;
  }
  // 对于typeof返回结果是object的，再进行如下的判断，正则返回结果
  return Object.prototype.toString.call(obj).replace(/^\[object (\S+)\]$/, '$1'); 
}
```

### 正则表达式
#### 匹配规则
| 规则 | 描述 |
| ---- | ---- |
| \ | 转义 |
| ^ | 匹配输入的开始 |
| $ | 匹配输入的结束 |
| * | 匹配前一个表达式 0 次或多次 |
| + | 匹配前面一个表达式 1 次或者多次。等价于 {1,} |
| ? | 匹配前面一个表达式 0 次或者 1 次。等价于{0,1} |
| . | 默认匹配除换行符之外的任何单个字符 |
| x(?=y) | 匹配'x'仅仅当'x'后面跟着'y'。这种叫做先行断言 |
| (?<=y)x | 匹配'x'仅当'x'前面是'y'.这种叫做后行断言 |
| x(?!y) | 仅仅当'x'后面不跟着'y'时匹配'x'，这被称为正向否定查找 |
| (?<!y)x | 仅仅当'x'前面不是'y'时匹配'x'，这被称为反向否定查找 |
| x\|y | 匹配‘x’或者‘y’ |
| {n} | n 是一个正整数，匹配了前面一个字符刚好出现了 n 次 |
| {n,} | n是一个正整数，匹配前一个字符至少出现了n次 |
| {n,m} | n 和 m 都是整数。匹配前面的字符至少n次，最多m次 |
| [xyz] | 一个字符集合。匹配方括号中的任意字符 |
| [^xyz] | 匹配任何没有包含在方括号中的字符 |
| \b | 匹配一个词的边界，例如在字母和空格之间 |
| \B | 匹配一个非单词边界 |
| \d | 匹配一个数字 |
| \D | 匹配一个非数字字符 |
| \f | 匹配一个换页符 |
| \n | 匹配一个换行符 |
| \r | 匹配一个回车符 |
| \s | 匹配一个空白字符，包括空格、制表符、换页符和换行符 |
| \S | 匹配一个非空白字符 |
| \w | 匹配一个单字字符（字母、数字或者下划线） |
| \W | 匹配一个非单字字符 |

***\b***
就用 "It's a nice day today." 举例说明：  

正确的正则：`\bnice\b`  

分析：第一个 \b 前面一个字符是空格，后面一个字符是 'n'，不全是 \w，所以可以匹配出 'n' 是一个单词的开头。第二个 \b 前面一个字符是 'e'，后面一个字符是空格，不全是 \w，可以匹配出 'e' 是一个单词的结尾。所以，合在一起，就能匹配出以 'n' 开头以 'e' 结尾的单词，这里就能匹配出 "nice" 这个单词。

**参考链接**
* <https://www.cnblogs.com/litmmp/p/4925374.html>
  
#### 正则表达式标记
| 标志 | 描述 |
| ---- | ---- |
| g | 全局搜索 |
| i | 不区分大小写搜索 |
| m | 多行搜索 |
| s | 允许 `.` 匹配换行符 |
| u | 使用 `unicode` 码的模式进行匹配 |
| y | 执行“粘性(`sticky`)”搜索,匹配从目标字符串的当前位置开始 |

***y***
全局匹配和粘滞匹配都会从字符串中由正则对象的 `lastIndex` 属性的值指定的偏移位置处开始匹配,但区别是:粘滞匹配中,`^` 元字符的意义变了,它代表的不是整个字符串的开头位置,而代表的就是这个偏移位置.所以上面的两次匹配都能成功.同时,每个粘滞正则中不管第一个字符是不是 `^` 元字符,都会被隐式的加上 `^`  
`/o/y.test("foo")`  
相当于  
`/^o/y.test("foo") `

```js
var re = /o/y;           //相当于/^o/y
print(re.test("foo"));   //false
print(re.lastIndex);     //0
re.lastIndex = 1;        //手动跳过了第一个字符f,^现在匹配的位置就是f和o之间的位置,所以^o能匹配.
print(re.test("foo"));   //true
print(re.lastIndex);     //2,^现在匹配的位置就是o和o之间的位置,所以^o能匹配.
print(re.test("foo"));   //true
print(re.lastIndex);     //3
print(re.test("foo"));   //false
print(re.lastIndex);     //3
```
**参考链接**
* <https://www.cnblogs.com/ziyunfei/archive/2012/12/07/2807313.html>

#### 贪婪模式
例： `const reg = /ab{1,3}c/
`  
在匹配过程中，尝试可能的顺序是从多往少的方向去尝试。首先会尝试bbb，然后再看整个正则是否能匹配。不能匹配时，吐出一个b，即在bb的基础上，再继续尝试，以此重复
```js
const string = "12345";
const regx = /(\d{1,3})(\d{1,3})/;
console.log( string.match(reg) );
// => ["12345", "123", "45", index: 0, input: "12345"]
```

#### 懒惰模式
惰性量词就是在贪婪量词后面加个问号。表示尽可能少的匹配
```js
var string = "12345";
var regex = /(\d{1,3}?)(\d{1,3})/;
console.log( string.match(regex) );
// => ["1234", "1", "234", index: 0, input: "12345"]
```

#### 匹配方法
* 字符串（str）方法：match、matchAll、search、replace、split
* 正则对象下（regexp）的方法：test、exec

-----
**参考链接**
* <https://vue3js.cn/interview/JavaScript/regexp.html>
* <https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions>

### 尾递归
#### 递归
普通递归形式写的用来计算 n 的阶乘的函数：
```js
function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}
```
计算 factorial(6) 的时候，会产生如下展开：
```js
6 * factorial(5)
6 * (5 * factorial(4))
6 * (5 * (4 * factorial(3))))
// two thousand years later...
6 * (5 * (4 * (3 * (2 * (1 * 1)))))) // <= 最终的展开
```
展开之后，会进行真正的计算  
```js
// 优化斐波那契数列
function fib(n, prev1 = 1, prev2 = 1){
  if(n <= 1) {
    return prev1;
  }
  return fib(n - 1, prev2, prev1 + prev2);
}
// or
function fib(n, prev1 = 1, prev2 = 1){
  if(n <= 2) {
    return prev2;
  }
  return fib(n - 1, prev2, prev1 + prev2);
}
```

普通递归的问题在于展开的时候会产生非常大的中间缓存，而每一层的中间缓存都会占用我们宝贵的栈上空间，所有导致了当这个 n 很大的时候，栈上空间不足则会产生“爆栈”的情况

#### 尾递归
尾递归，即在函数尾位置调用自身（或是一个尾调用本身的其他函数等等）。尾递归也是递归的一种特殊情形。尾递归是一种特殊的尾调用，即在尾部直接调用自身的递归函数  

尾递归在普通尾调用的基础上，多出了2个特征：  
* 在尾部调用的是函数自身
* 可通过优化，使得计算仅占用常量栈空间  
  
在递归调用的过程当中系统为每一层的返回点、局部量等开辟了栈来存储，递归次数过多容易造成栈溢出  
```js
function factorial(n, total) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}
```
这时候，我们就可以使用尾递归，可通过优化，使得计算仅占用常量栈空间  
如上述例子使用尾递归优化后的调用栈为：  
```js
fact(6, 1) // 1 是 fact(0) 的值，我们需要手动写一下
fact(5, 6)
fact(4, 30)
fact(3, 120)
fact(2, 360)
fact(1, 720)
720 // <= 最终的结果
```
在展开的过程中计算并缓存了结果，使用的常量空间变小  
**注意：尾递归在n过大时也会发生调用栈溢出的情况，因为函数调用栈会一直存在**

### js本地存储方式
#### 区别
关于`cookie`、`sessionStorage`、`localStorage`三者的区别主要如下：
* 存储大小：`cookie`数据大小不能超过4k，`sessionStorage`和`localStorage`虽然也有存储大小的限制，但比`cookie`大得多，可以达到5M或更大
* 有效时间：`localStorage`存储持久数据，浏览器关闭后数据不丢失除非主动删除数据；` sessionStorage`数据在当前浏览器窗口关闭后自动删除；`cookie`设置的`cookie`过期时间之前一直有效，即使窗口或浏览器关闭
* 数据与服务器之间的交互方式，`cookie`的数据会自动的传递到服务器，服务器端也可以写`cookie`到客户端； `sessionStorage`和`localStorage`不会自动把数据发给服务器，仅在本地保存
  
#### 应用场景
* 标记用户与跟踪用户行为的情况，推荐使用`cookie`
* 适合长期保存在本地的数据（令牌），推荐使用`localStorage`
* 敏感账号一次性登录，推荐使用`sessionStorage`
* 存储大量数据的情况、在线文档（富文本编辑器）保存编辑历史的情况，推荐使用`indexedDB`

#### Cookie的属性
1. name/value
2. Domain：决定cookie在哪个域是有效的。决定该域在发送请求时是否携带cookie。对子域生效
3. path：cookie的有效路径。和Domain类型，对子路径有效
4. expires/max-age:cookie的有效期
5. size: cookie的大小
6. httpOnly:若值为true，不允许通过脚本去修改这个cookie值
7. secure：若值为true，只会在https或ssl等安全协议中传输cookie
8. sameSite: 用来限制第三方cookie，可以预防CSRF攻击
9. Priority: cookie优先级，当cookie数量超出时，低优先级的cookie会被优先清除

### 函数式编程
#### 定义
主要的编程范式有：命令式编程，声明式编程  
* 命令式编程：关注实现的具体步骤（过程式编程是命令式编程的子集）
* 声明式：关注最后的结果（函数式编程是声明式编程的子集）

相比命令式编程，声明式编程更加强调程序执行的结果而非执行的过程，倡导利用若干简单的执行单元让计算结果不断渐进，逐层推导复杂的运算，而非设计一个复杂的执行过程
```js
// 命令式编程
var array = [0, 1, 2, 3]
for(let i = 0; i < array.length; i++) {
    array[i] = Math.pow(array[i], 2)
}

// 声明式编程 SQL语句就是声明式编程
selec * from table where num>5
// 函数式方式
[0, 1, 2, 3].map(num => Math.pow(num, 2))
```
简单来讲，就是要把过程逻辑写成函数，定义好输入参数，只关心它的输出结果

#### 高阶函数
满足以下条件之一，就是高阶函数
1. 函数作为参数被传递
```js
[1,2,3,4].sort((a-b) => a>b)
```
2. 函数作为返回值输出

#### 函数式的优缺点
**优点**
* 更好的管理状态：因为它的宗旨是无状态，或者说更少的状态，能最大化的减少这些未知、优化代码、减少出错情况
* 更简单的复用：固定输入->固定输出，没有其他外部变量影响，并且无副作用。这样代码复用时，完全不需要考虑它的内部实现和外部影响
* 更优雅的组合：往大的说，网页是由各个组件组成的。往小的说，一个函数也可能是由多个小函数组成的。更强的复用性，带来更强大的组合性
* 隐性好处。减少代码量，提高维护性

**缺点**
* 性能：函数式编程相对于指令式编程，性能绝对是一个短板，因为它往往会对一个方法进行过度包装，从而产生上下文切换的性能开销
* 资源占用：在 JS 中为了实现对象状态的不可变，往往会创建新的对象，因此，它对垃圾回收所产生的压力远远超过其他编程方式
* 递归陷阱：在函数式编程中，为了实现迭代，通常会采用递归操作

### js精度丢失
#### 十进制小数转二进制
**乘2取整法**
1. 将小数的小数部分取出，乘以2，将得到的结果中的整数部分作为二进制小数的项
2. 得到结果中的小数部分重复第一步的步骤
3. 直到某一步乘以2的值的小数部分为0，或者小数部分形成循环小数则停止

比如 0.1875这个十进制小数，计算转换为二进制的过程：
| 乘以2 | 积 | 整数部分 | 小数部分 |
| ----- | -- | ------- | ------- |
| 0.1875 * 2 | 0.375 | 0 | 0.375 |
| 0.375*2 | 0.75 | 0 | 0.75 |
| 0.75*2 | 1.5 | 1 | 0.5 |
| 0.5*2 | 1 | 1 | 0 |

得到结果为：0011

#### 双精度浮点数
javascript以64位双精度浮点数存储所有Number类型值 即计算机最多存储64位二进制数  
对于一个整数，我们可以很方便的在十进制与二进制中转换，但是对于一个浮点数来说不是这么容易——因为小数点的存在。对于浮点数来说，小数点的位置不是固定的（小数点后面的数的个数不定），所以如何存储小数点是一个挑战。后来人们想出用科学计数法通常如这般： $1.011*2^4$ 来表示浮点数，这样的好处是：小数点的位置固定下来了  

举例：27.0表示成二进制为：11011.0 用科学计数法表示为 $1.10110*2^4$
对于double型数据(双精度浮点数)，其长度是8个字节(大小)，  
1. 右边52位用来表示小数点后面的数字
2. 中间11位表示e(exponent)小数点移动的位数
3. 左边一位用来表示正负

>举例：
>27.5 转换为二进制11011.1
>11011.1转换为科学记数法 $1.10110*2^4$  
>符号位为1(正数)  
>指数位为4+指数偏移量1023 即1027 因为它是十进制的需要转换为二进制即 10000000011  
>小数部分为10111，补够52位即： 1011 1000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000  
>指数偏移量为 1023  
>所以27.5存储为计算机的二进制标准形式为  
>符号位+指数位+小数部分 (阶数)  
>0 + 10000000011 + 1011 1000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000  
>即：  
>0100 0000 0011 1011 1000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000  
>正好64位

计算机存储双精度浮点数需要先把十进制数转换为二进制的科学记数法的形式，然后计算机以自己的规则{符号位+(指数位+指数偏移量的二进制)+小数部分}存储二进制的科学记数法,因为存储时有位数限制（64位），并且某些十进制的浮点数在转换为二进制数时会出现无限循环，会造成二进制的舍入操作(0舍1入)，当再转换为十进制时就造成了计算误差

#### 解决方案
* 第三方库 bignumber.js - 将数字作为字符串处理；math.js
* toFixed() - 对于小数最后一位为5时进位不正确
```js
//firefox/chrome中toFixed 兼容性问题
1.35.toFixed(1) // 1.4 正确
1.335.toFixed(2) // 1.33  错误
1.3335.toFixed(3) // 1.333 错误
1.33335.toFixed(4) // 1.3334 正确
1.333335.toFixed(5)  // 1.33333 错误
1.3333335.toFixed(6) // 1.333333 错误
```
* 展示类： 使用 toPrecision 凑整并 parseFloat 转成数字后再显示
* ES6在Number对象上新增了一个极小的常量——Number.EPSILON，引入一个这么小的量，目的在于为浮点数计算设置一个误差范围，如果误差能够小于Number.EPSILON，我们就可以认为结果是可靠的
```js
// 检查误差函数
function withinErrorMargin (left, right) {
    return Math.abs(left - right) < Number.EPSILON
}
withinErrorMargin(0.1+0.2, 0.3)
```

-----
**参考链接**
* <https://zhuanlan.zhihu.com/p/100353781>
* <https://blog.csdn.net/hjb2722404/article/details/120514744>

### require 和 import 区别
1. import在代码编译时被加载，所以必须放在文件开头，require在代码运行时被加载，所以require理论上可以运用在代码的任何地方，所以import性能更好。
2. import引入的对象被修改时，源对象也会被修改，相当于浅拷贝，require引入的对象被修改时，源对象不会被修改，官网称值拷贝，我们可以理解为深拷贝。
3. import有利于tree-shaking（移除JavaScript上下文中未引用的代码），require对tree-shaking不友好。
4. import会触发代码分割（把代码分离到不同的bundle中，然后可以按需加载或者并行加载这些文件），require不会触发。
5. import是es6的一个语法标准，如果要兼容浏览器的话必须转化成es5的语法，require 是 AMD规范引入方式

### 图片懒加载原理
先设置图片的data-set属性(当然也可以是其他任意的，只要不会发送http请求就行了，作用就是为了存取值)值为其图片路径，由于不是src，所以不会发送http请求。 然后我们计算出页面scrollTop的高度和浏览器的高度之和，如果图片举例页面顶端的坐标Y(相对于整个页面，而不是浏览器窗口)小于前两者之和，就说明图片就要显示出来了(合适的时机，当然也可以是 其他情况)，这时候再将 data-set属性替换为src属性即可

### MVVM框架与MVC框架
#### 区别
mvc 和 mvvm 其实区别并不大。都是一种设计思想，主要区别如下：
1. mvc 中 Controller演变成 mvvm 中的 viewModel
2. mvvm 通过数据来驱动视图层的显示而不是节点操作。
3. mvc中Model和View是可以直接打交道的，造成Model层和View层之间的耦合度高。而mvvm中Model和View不直接交互，而是通过中间桥梁ViewModel来同步
4. mvvm主要解决了:mvc中大量的DOM 操作使页面渲染性能降低，加载速度变慢，影响用户体验

MVC中的`Controller`控制数据流向模型对象，并在数据变化时更新视图。使视图与模型分离开  

MVVM本质上就是MVC的改进版。Model指的是后端传递的数据。View指的是所看到的页面。ViewModel是mvvm模式的核心，它是连接view和model的桥梁。它有两个方向：一是将【模型】转化成【视图】，即将后端传递的数据转化成所看到的页面。实现的方式是：数据绑定。二是将【视图】转化成【模型】，即将所看到的页面转化成后端的数据。实现的方式是：DOM 事件监听。这两个方向都实现的，我们称之为数据的双向绑定

-----
**参考链接**
* <https://blog.csdn.net/qq_43225030/article/details/106519084>

### js继承
#### 原型链继承
```js
function ParentClass() {
  this.name = '一碗周'
}
ParentClass.prototype.getName = function () {
  return this.name
}
// 定义子类，将来用于继承父类
function ChildClass() {}
// * 将子类的原型指向父类的实例化，子类拥有父类实例化后的内容
ChildClass.prototype = new ParentClass()
// 将子类进行实例化
var child = new ChildClass()
console.log(child.getName()) // 一碗周
```
缺点: 多个实例会导致原型对象上的内容是共享的，内容之间会互相影响

#### 构造函数继承
```js
function Parent() {
  // 父级对象
  this.parent = 'parent'
}
Parent.prototype.name = '一碗周' // 为 Parent 父级对象的原型增加属性

function Child() {
  // 子级对象
  this.child = 'child'
  Parent.call(this) // 使用 call() 或者 apply() 方法调用父级构造函数 实现继承。
}
const child = new Child()
console.log(child)
console.log(child.name) // undefined     // 不会继承父类的原型
```
优点：避免了引用类型的实例被所有对象共享  
缺点：因为所有的方法都定义在了构造函数中，不会继承原型对象，而且每实例化一个对象之后都会重新创建一遍这些方法，占用内存空间

#### 组合式继承
思路：
* 使用原型链或原型式继承实现对原型的属性和方法的继承。
* 通过结构构造函数实现对实例对象的属性的继承

```js
// 父级对象
function Parent() {
  this.parent = 'parent'
}
// 为 Parent 父级对象的原型增加属性
Parent.prototype.name = '一碗周'
// 子级对象
function Child() {
  this.child = 'child'
  // 使用 call() 或者 apply() 方法调用父级构造函数 实现继承。
  Parent.call(this)
}
// 解决不会继承构造函数的原型对象的问题
Child.prototype = Parent.prototype

const child = new Child()
console.log(child.name) // 一碗周
```
缺点： 由于子类原型和父类原型指向同一个对象，对子类原型的操作会影响到父类原型

#### Object.create()
```js
var person = {
  name: '一碗周',
  friends: ['张三', '李四', '王五'],
}

var anotherPerson = Object.create(person)
anotherPerson.name = '一碗甜'
anotherPerson.friends.push('赵六')

console.log(person.friends) // [ '张三', '李四', '王五', '赵六' ]
```
缺点：多个实例会导致原型对象上的内容是共享的，内容之间会互相影响

#### 寄生式继承
```js
var person = {
  name: '一碗周',
  friends: ['张三', '李四', '王五'],
}

function createAnother(original) {
  var clone = Object.create(original) // 通过调用 object() 函数创建一个新对象
  clone.sayMe = function () {
    // 以某种方式来增强对象
  }
  return clone // 返回这个对象
}

var anotherPerson = createAnother(person)
anotherPerson.sayMe()
```
缺点：多个实例会导致原型对象上的内容是共享的，内容之间会互相影响

#### 寄生组合式继承
```js
function inheritPrototype(ChildClass, ParentClass) {
  var prototype = Object.create(ParentClass.prototype) // 创建对象，创建父类原型的一个副本
  // 修改创建的父类原型副本的 constructor 并将子类的 prototype 指向这个类，形成与父类无关联的类
  prototype.constructor = ChildClass
  ChildClass.prototype = prototype
}

// 父类初始化实例属性和原型属性
function ParentClass(name) {
  this.name = name
  this.colors = ['red', 'blue', 'green']
}
ParentClass.prototype.sayName = function () {
  console.log(this.name)
}

// 借用构造函数传递增强子类实例属性（支持传参和避免篡改）
function ChildClass(name, age) {
  // 拷贝父类所有自有属性
  ParentClass.call(this, name)
  this.age = age
}

// 将父类原型指向子类
inheritPrototype(ChildClass, ParentClass)

// 新增子类原型属性
ChildClass.prototype.sayAge = function () {
  console.log(this.age)
}

var instance1 = new ChildClass('一碗周', 19)
var instance2 = new ChildClass('一碗甜', 18)

instance1.colors.push('black')
console.log(instance1.colors) // [ 'red', 'blue', 'green', 'black' ]
instance1.sayName() // 一碗周
instance2.colors.push('yellow')
console.log(instance2.colors) // [ 'red', 'blue', 'green', 'yellow' ]
```

#### ES6 class继承
ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply(this)）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this。 （错误，写法问题导致的） 
* 除了私有属性，父类的所有属性和方法，都会被子类继承，其中包括静态方法.
* 凡是被static修饰的属性和方法都是静态方法和属性,只能被类名调用,不能被实例化对象调用

**一个类中，static 普通方法 箭头函数重名时，会先调用箭头函数**

-----
**参考链接**
* <https://juejin.cn/post/7033952613389631501>
* <https://segmentfault.com/a/1190000016708006>

### web常见攻击方式
#### XSS (Cross Site Scripting) 跨站脚本攻击
恶意脚本攻击
```html
<input type="text" value="<%= getParameter("keyword") %>">
<button>搜索</button>
<div>
  您搜索的关键词是：<%= getParameter("keyword") %>
</div>
```
当 `keyword = "><script>alert('XSS');</script>` 时，html被解析成
```html
<input type="text" value=""><script>alert('XSS');</script>">
<button>搜索</button>
<div>
  您搜索的关键词是："><script>alert('XSS');</script>
</div>
```
XSS攻击的两大要素：
* 攻击者提交而恶意代码: 前端过滤
* 浏览器执行恶意代码：数据库存储前过滤

#### CSRF（Cross-site request forgery）跨站请求伪造
攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求  
一个典型的CSRF攻击有着如下的流程：
* 受害者登录a.com，并保留了登录凭证（Cookie）
* 攻击者引诱受害者访问了b.com
* b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带a.com的Cookie
* a.com接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求
* a.com以受害者的名义执行了act=xx
* 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让a.com执行了自己定义的操作

跨站请求可以用各种方式：图片URL、超链接、CORS、Form提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪  
防止csrf常用方案如下：
* 阻止不明外域的访问
  * 同源检测
  * Samesite Cookie
* 提交时要求附加本域才能获取的信息
  * CSRF Token
  * 双重Cookie验证

#### SQL注入攻击
通过将恶意的 Sql查询或添加语句插入到应用的输入参数中，再在后台 Sql服务器上解析执行进行攻击

-----
**参考链接**
* <https://vue3js.cn/interview/JavaScript/security.html#%E4%B8%80%E3%80%81%E6%98%AF%E4%BB%80%E4%B9%88>

### 设计模式
#### 策略模式
**业务场景：当代码中有诸多`if...else`条件分支 ，可以使用策略模式进行优化**  
缺点：
1. 对已有`if...else`进行修改时，只能修改原有代码；
2. 当条件过多时，代码会变得臃肿

上述两点违背了代码的**开闭原则**和**单一原则**
* 开闭原则：对于拓展是开放的，对于修改时封闭的（每次改变都需要修改原有代码）
* 单一原则：规定一个类应该只有一个发生变化的原因，一个类只负责一项职责

##### 策略模式的使用
优化前代码
```js
/* 获取价格
预售：8折
促销：原价减10
默认：原价 */
function getPrice(originalPrice, status) {
  if( status === 'pre-sale') {
    return originalPrice * 0.8;
  }
  if(status === 'sale') {
    return originalPrice - 10;
  }
  if(status === 'default') {
    return originalPrice;
  }
  // 如果增加一种情况，618购物节时，5折
  // 则需要修改原有代码
  if(status === 'shop') {
    return originalPrice * 0.5;
  }
}
```
优化后代码
```js
const statusMap = {
  'pre-sale': getPreSalePrice;
  'sale': getSalePrice;
  'default': getDefaultPrice;
  // 增加情况时，添加映射
  'shop': getShopPrice;
}
function getPreSalePrice(originalPrice) {
  return originalPrice * 0.8;
}
function getSalePrice(originalPrice) {
  return originalPrice - 10;
}
function getDefaultPrice(originalPrice) {
  return originalPrice;
}
// 简化代码，增加情况时，不会修改原有类代码和逻辑
function getPrice(originalPrice, status) {
  return statusMap[status](originalPrice);
}
// 增加情况时,添加方法
function getShopPrice(originalPrice) {
  return originalPrice * 0.5;
}
```

参考链接：<https://juejin.cn/post/7113451841001619463>

#### 观察者模式
观察者模式和发布-订阅模式的区别：前者没有中间件，后者有中间件  
使用场景：
1. click监听事件，`addEventlistenner('click', () => {})`（观察者）
2. vue双向绑定（发布订阅，中间件-Dep）
3. vue emit、on 事件处理（观察者）

#### 单例模式
限制类只能实例化一次，一个类只有一个实例，并提供一个全局访问点（自动实例化）  
**唯一**和**全局访问**类似js的全局对象  
1. 避免频繁创建和销毁实例
2. 确保访问的对象都是同一个

##### 单例模式的使用
进行类实例化时，如果实例对象存在,则返回该实例，不存在则创建后返回

##### 应用场景
1. 引入第三方库
2. 全局状态管理Store(vuex,redux)
3. vue2:通过 `new Vue()`创建一个根实例，其他单文件组件都会成为根实例的子实例。共享全局指令、全局组件
   1. vue3： 通过`createApp()`可以创建多个vue实例，不同实例相互隔离。适用于大型应用的多人员协作开发
4. 弹窗、vue-$message,$loading

单例模式适用于重复访问的模块，避免重复调用和加载

#### 工厂模式
工厂模式在一个函数中创建多个相似对象，子类调用时，根据入参返回需要的对象实例。工厂模式不暴露创建对象的具体逻辑。   
优点：**解决多个相似的问题，减少大量冗余代码**（多次调用，传参冗余等）

##### 简单工厂模式
```js
class User {
  //构造器
  constructor(opt) {
    this.name = opt.name;
    this.viewPage = opt.viewPage;
  }

  //静态方法
  static getInstance(role) {
    switch (role) {
      case 'superAdmin':
        return new User({ name: '超级管理员', viewPage: ['首页', '通讯录', '发现页', '应用数据', '权限管理'] });
        break;
      case 'admin':
        return new User({ name: '管理员', viewPage: ['首页', '通讯录', '发现页', '应用数据'] });
        break;
      case 'user':
        return new User({ name: '普通用户', viewPage: ['首页', '通讯录', '发现页'] });
        break;
      default:
        throw new Error('参数错误, 可选参数:superAdmin、admin、user')
    }
  }
}

//调用
let superAdmin = User.getInstance('superAdmin');
let admin = User.getInstance('admin');
let normalUser = User.getInstance('user');
```

##### 工厂方法模式
将创建对象的工作推到子类中进行
```js
// JS设计模式之工厂方法模式
function factory(role){
  if(this instanceof factory){
      var a = new this[role]();
      return a;
  }else{
      return new factory(role);
  }
}

factory.prototype={
  "superAdmin":function(){
      this.name="超级管理员";
      this.viewPage=["首页","发现页","通讯录","应用数据","权限管理"];
  },
  "admin":function(){
      this.name="管理员";
      this.viewPage=["首页","发现页","通讯录","应用数据"];
  },
  "user":function(){
      this.name="普通用户";
      this.viewPage=["首页","发现页","通讯录"];
  }
}

let superAdmin = factory("superAdmin");
console.log(superAdmin);
let admin = factory("admin");
console.log(admin);
let user = factory("user");
console.log(user);
```

##### 抽象工厂模式
简单工厂和工厂方法类似与生产产品，抽象工厂则用于生产工厂
```js
class User {
  constructor(type) {
    if (new.target === User) {
      throw new Error('抽象类不能实例化!')
    }
    this.type = type;
  }
}

class UserOfWechat extends User {
  constructor(name) {
    super('wechat');
    this.name = name;
    this.viewPage = ['首页', '通讯录', '发现页']
  }
}

class UserOfQq extends User {
  constructor(name) {
    super('qq');
    this.name = name;
    this.viewPage = ['首页', '通讯录', '发现页']
  }
}

class UserOfWeibo extends User {
  constructor(name) {
    super('weibo');
    this.name = name;
    this.viewPage = ['首页', '通讯录', '发现页']
  }
}

function getAbstractUserFactory(type) {
  // new this[type]() // 这样是不是更好？可以直接返回实例对象
  switch (type) {
    case 'wechat':
      return UserOfWechat;
      break;
    case 'qq':
      return UserOfQq;
      break;
    case 'weibo':
      return UserOfWeibo;
      break;
    default:
      throw new Error('参数错误, 可选参数:superAdmin、admin、user')
  }
}

let WechatUserClass = getAbstractUserFactory('wechat');
let QqUserClass = getAbstractUserFactory('qq');
let WeiboUserClass = getAbstractUserFactory('weibo');

let wechatUser = new WechatUserClass('微信小李');
let qqUser = new QqUserClass('QQ小李');
let weiboUser = new WeiboUserClass('微博小李');
```

**简单总结**  
简单工厂模式就是你给工厂什么，工厂就给你生产什么；  
工厂方法模式就是你找工厂生产产品，工厂是外包给下级分工厂来代加工，需要先评估一下能不能代加工；能做就接，不能做就找其他工厂；  
抽象工厂模式就是工厂接了某项产品订单但是做不了，上级集团公司新建一个工厂来专门代加工某项产品；

##### 工厂模式应用实例
1. jquery
```js
class jQuery {
    constructor(selector) {
        super(selector)
    }
    //  ....
}

window.$ = function(selector) {
    return new jQuery(selector)
}
```
2. React的createElement()  (vue里面的createElement同样)
```js
React.createElement('div', {/* ... */})
React.createElement('img', {/* ... */})
```

#### 装饰者模式
在不改变对象自身的基础上，动态地给某个对象添加一些额外的职责
##### 应用实例
在函数执行前后添加新的方法
1. vue 路由钩子


## CSS
### 盒子模型
一个盒子由四个部分组成：content、padding、border、margin  
**标准盒子模型**  
1. 盒子总宽度 = width + padding + border + margin;
2. 盒子总高度 = height + padding + border + margin

width/height 只是内容高度，不包含 padding 和 border值  

**IE 怪异盒子模型**  
1. 盒子总宽度 = width + margin;
2. 盒子总高度 = height + margin

width/height 包含了 padding和 border值

#### Box-sizing
```css
box-sizing: content-box|border-box|inherit:
```
* content-box 默认值，元素的 width/height 不包含padding，border，与标准盒子模型表现一致
* border-box 元素的 width/height 包含 padding，border，与怪异盒子模型表现一致
* inherit 指定 box-sizing 属性的值，应该从父元素继承

### css选择器
* id选择器（#box），选择id为box的元素
* 类选择器（.one），选择类名为one的所有元素
* 标签选择器（div），选择标签为div的所有元素
* 后代选择器（#box div），选择id为box元素内部所有的div元素
* 子选择器（.one>one_1），选择父元素为.one的所有.one_1的元素
* 相邻同胞选择器（.one+.two），选择紧接在.one之后的所有.two元素
* 群组选择器（div,p），选择div、p的所有元素
* 伪类选择器
```css
:link ：选择未被访问的链接
:visited：选取已被访问的链接
:active：选择活动链接
:hover ：鼠标指针浮动在上面的元素
:focus ：选择具有焦点的
:first-child：父元素的首个子元素
```
* 伪元素选择器
```css
:first-letter ：用于选取指定选择器的首字母
:first-line ：选取指定选择器的首行
:before : 选择器在被选元素的内容前面插入内容
:after : 选择器在被选元素的内容后面插入内容
```
#### 选择器优先级
>内联 > ID选择器 > 类选择器 > 标签选择器

### css3新特性
* box-shadow
* border-radius
* background-clip
  * background-clip: border-box; 背景从border开始显示
  * background-clip: padding-box; 背景从padding开始显示
  * ackground-clip: content-box; 背景显content区域开始显示
  * background-clip: no-clip; 默认属性，等同于border-box
* background-size
* word-wrap: 文字换行
* text-overflow:设置或检索当前行超过指定容器的边界时如何显示，属性有两个值选择：
  * clip：修剪文本
  * ellipsis：显示省略符号来代表被修剪的文本
* 新增颜色表达式：rgba和hsla
  * rgba分为两部分，rgb为颜色值，a为透明度
  * hsla分为四部分，h为色相，s为饱和度，l为亮度，a为透明度
* transition 过渡
```css
transition： CSS属性，花费时间，效果曲线(默认ease)，延迟时间(默认0)
transition-property: width; 
transition-duration: 1s;
transition-timing-function: linear;
transition-delay: 2s;
```
* transform属性: 旋转，缩放，倾斜或平移给定元素
* animation 动画
* flex弹性布局、Grid栅格布局
* 媒体查询： `@media screen and (max-width: 1920px) { ... }`

### 回流与重绘
* 回流：布局引擎会根据各种样式计算每个盒子在页面上的大小与位置
* 重绘：当计算好盒模型的位置、大小及其他属性后，浏览器根据每个盒子特性进行绘制

#### 浏览器解析渲染机制
* 解析HTML，生成DOM树，解析CSS，生成CSSOM树
* 将DOM树和CSSOM树结合，生成渲染树(Render Tree)
* Layout(回流):根据生成的渲染树，进行回流(Layout)，得到节点的几何信息（位置，大小）
* Painting(重绘):根据渲染树以及回流得到的几何信息，得到节点的绝对像素
* Display:将像素发送给GPU，展示在页面上

#### 浏览器优化机制
由于每次重排都会造成额外的计算消耗，因此大多数浏览器都会通过队列化修改并批量执行来优化重排过程。浏览器会将修改操作放入到队列里，直到过了一段时间或者操作达到了一个阈值，才清空队列  
**当获取布局信息的操作的时候，会强制队列刷新，包括前面讲到的offsetTop等方法都会返回最新的数据**  
因此浏览器不得不清空队列，触发回流重绘来返回正确的值

#### 减少回流与重绘
* 如果想设定元素的样式，通过改变元素的 class 类名 (尽可能在 DOM 树的最里层)
* 避免设置多项内联样式
* 应用元素的动画，使用 position 属性的 fixed 值或 absolute 值(如前文示例所提)
* 避免使用 table 布局，table 中每个元素的大小以及内容的改动，都会导致整个 table 的重新计算
* 对于那些复杂的动画，对其设置 position: fixed/absolute，尽可能地使元素脱离文档流，从而减少对其他元素的影响
* 使用css3硬件加速，可以让transform、opacity、filters这些动画不会引起回流重绘
* 避免使用 CSS 的 JavaScript 表达式

#### 无法避免时的优化方案
1. 多次修改一个把元素布局的时候
```js
// 优化前
const el = document.getElementById('el')
for(let i=0;i<10;i++) {
    el.style.top  = el.offsetTop  + 10 + "px";
    el.style.left = el.offsetLeft + 10 + "px";
}

// 优化后
// 缓存offsetLeft与offsetTop的值
const el = document.getElementById('el')
let offLeft = el.offsetLeft, offTop = el.offsetTop

// 在JS层面进行计算
for(let i=0;i<10;i++) {
  offLeft += 10
  offTop  += 10
}

// 一次性将计算结果应用到DOM上
el.style.left = offLeft + "px"
el.style.top = offTop  + "px"
```
2. 避免改变样式，使用类名去合并样式
3. 设置元素属性display: none，将其从页面上去掉，然后再进行后续操作，这些后续操作也不会触发回流与重绘，这个过程称为离线操作

-----
**参考链接**
* <https://vue3js.cn/interview/css/layout_painting.html#%E4%B8%80%E3%80%81%E6%98%AF%E4%BB%80%E4%B9%88>

### css性能优化
* 内联首屏关键CSS： 在打开一个页面，页面首要内容出现在屏幕的时间影响着用户的体验，而通过内联css关键代码能够使浏览器在下载完html后就能立刻渲染
* 异步加载CSS： 在CSS文件请求、下载、解析完成之前，CSS会阻塞渲染，浏览器将不会渲染任何已处理的内容。可以采取异步加载的方案。
* 资源压缩
* 合理使用选择器
* 减少使用昂贵的属性： 在页面发生重绘的时候，昂贵属性如box-shadow/border-radius/filter/透明度/:nth-child等，会降低浏览器的渲染性能
* 不要使用@import

-----
**参考链接**
* <https://vue3js.cn/interview/css/css_performance.html>

### css预编语言
扩充了 Css 语言，增加了诸如变量、混合（mixin）、函数等功能，让 Css 更易维护、方便  
本质上，预处理是Css的超集  
包含一套自定义的语法及一个解析器，根据这些语法定义自己的样式规则，这些规则最终会通过解析器，编译生成对应的 Css 文件  
预编处理器： sass、less、stylus

## webpack
`webpack` 是一个用于现代`JavaScript`应用程序的**静态模块打包工具**。这里的静态模块指的是开发阶段，可以被 `webpack` 直接引用的资源（可以直接被获取打包进bundle.js的资源）  
webpack编译会创建两个核心对象：
* compiler：包含了 webpack 环境的所有的配置信息，包括 options，loader 和 plugin，和 webpack 整个生命周期相关的钩子
* compilation：作为 plugin 内置事件回调函数的参数，包含了当前的模块资源、编译生成资源、变化的文件以及被跟踪依赖的状态信息。当检测到一个文件变化，一次新的 Compilation 将被创建

### webpack构建流程
* 初始化流程：从配置文件和 Shell 语句中读取与合并参数，并初始化需要使用的插件和配置插件等执行环境所需要的参数
* 编译构建流程：从 Entry 发出，针对每个 Module 串行调用对应的 Loader 去翻译文件内容，再找到该 Module 依赖的 Module，递归地进行编译处理
* 输出流程：对编译后的 Module 组合成 Chunk，把 Chunk 转换成文件，输出到文件系统

### loader
在webpack内部中，任何文件都是模块，不仅仅只是js文件  
默认情况下，在遇到import或者require加载模块的时候，webpack只支持对js 和 json 文件打包  
像css、sass、png等这些类型的文件的时候，webpack则无能为力，这时候就需要配置对应的loader进行文件内容的解析

### plugin
* HtmlWebpackPlugin: 在打包结束后，⾃动生成⼀个 html 文件，并把打包生成的js 模块引⼊到该 html 中
```js
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
 ...
  plugins: [
     new HtmlWebpackPlugin({
       title: "My App",
       filename: "app.html",
       template: "./src/html/index.html"
     }) 
  ]
};
```
```html
<title><%=htmlWebpackPlugin.options.title%></title>
```
* DefinePlugin：允许在编译时创建配置的全局对象，是一个webpack内置的插件，不需要安装
```js
const { DefinePlugun } = require('webpack')
module.exports = {
 ...
    plugins:[
        new DefinePlugin({
            BASE_URL:'"./"'
        })
    ]
}
```
```html
<link rel="icon" href="<%= BASE_URL%>favicon.ico>"
```

#### loader 和 plugin 的区别
* loader 是文件加载器，能够加载资源文件，并对这些文件进行一些处理，诸如编译、压缩等，最终一起打包到指定的文件中
* plugin 赋予了 webpack 各种灵活的功能，例如打包优化、资源管理、环境变量注入等，目的是解决 loader 无法实现的其他事

### webpack 优化前端性能
* JS代码压缩: 默认terser
* CSS代码压缩
* Html文件代码压缩
* 文件大小压缩: gzip。减少http传输过程中的带宽消耗
* 图片压缩： 小于8k生成base64，内联。
* Tree Shaking：打包时删除无用（没被使用的）代码
* 代码分离： 按需加载
* 内联 chunk

关于webpack对前端性能的优化，可以通过**文件体积大小**入手，其次还可通过**分包的形式**、**减少http请求次数**等方式，实现对前端性能的优化

### 提高webpack的构建速度
* 优化 loader 配置: include、exclude,加快匹配
* 合理使用 resolve.extensions
* 优化 resolve.modules
* 优化 resolve.alias: 项目目录结构比较深的时候，一个文件的路径可能是./../../的形式.通过配置alias以减少查找过程
* 使用 DLLPlugin 插件： 可以共享，不经常改变的代码，抽成一个共享的库。这个库在之后的编译过程中，会被引入到其他项目的代码中
* 使用 cache-loader：将性能开销大的laoder进行缓存，提高二次构建速度
* terser 启动多线程
* 合理使用 sourceMap

优化webpack构建的方式有很多，主要可以从**优化搜索时间**、**缩小文件搜索范围**、**减少不必要的编译**等方面入手

## HTTP
### 常见状态码
状态码第一位数字决定了不同的响应状态，有如下：
* 1 表示消息
* 2 表示成功
  * 200（成功）：请求已成功，请求所希望的响应头或数据体将随此响应返回
* 3 表示重定向
  * 301（永久移动）：请求的网页已永久移动到新位置。 服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置
  * 302（临时移动）： 服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求
  * 305 （使用代理）： 请求者只能使用代理访问请求的网页。 如果服务器返回此响应，还表示请求者应使用代理
* 4 表示请求错误
  * 400（错误请求）： 服务器不理解请求的语法
  * 401（未授权）： 请求要求身份验证。 对于需要登录的网页，服务器可能返回此响应。
  * 403（禁止）： 服务器拒绝请求
  * 404（未找到）： 服务器找不到请求的网页
  * 405（方法禁用）： 禁用请求中指定的方法
  * 408（请求超时）： 服务器等候请求时发生超时
* 5 表示服务器错误
  * 500（服务器内部错误）：服务器遇到错误，无法完成请求
  * 501（尚未实施）：服务器不具备完成请求的功能。 例如，服务器无法识别请求方法时可能会返回此代码
  * 502（错误网关）： 服务器作为网关或代理，从上游服务器收到无效响应
  * 503（服务不可用）： 服务器目前无法使用（由于超载或停机维护）
  * 504（网关超时）： 服务器作为网关或代理，但是没有及时从上游服务器收到请求
  * 505（HTTP 版本不受支持）： 服务器不支持请求中所用的 HTTP 协议版本

### CDN
CDN全称叫做“Content Delivery Network”，中文叫内容分发网络  
在没有应用CDN时，我们使用域名访问某一个站点时的路径为  
>用户提交域名→浏览器对域名进行解释→DNS 解析得到目的主机的IP地址→根据IP地址访问发出请求→得到请求数据并回复  

域名解析，实际上就是解析出指定域名所对应的IP地址，或者该域名的一个CNAME  
服务器上的资源分为两种：静态资源与动态资源。
* 静态资源：这种资源通常是很少变动的，比如图片，视频，css，javascript等等
* 动态资源：这种资源不同用户不同时刻访问通常是不一样的，比如ftl，jsp等等

在每个服务器上部署静态资源，请求时通过CDN（CDN专用DNS服务器）找到距离用户ip地址最近的服务器上返回所请求的静态资源。降低了传输延迟，实现了网站加速。  
请求动态资源时（通过代理的方式）回源站取  

-----
**参考链接**
* <https://juejin.cn/post/6844903890706661389>
* <https://vue3js.cn/interview/http/CDN.html#%E4%BA%8C%E3%80%81%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90>

### DNS解析
由于 IP 地址具有不方便记忆并且不能显示地址组织的名称和性质等缺点，人们设计出了域名，并通过域名解析协议（DNS，Domain Name System）来将域名和 IP 地址相互映射  
域名其实是具有一定的层次结构的，从上到下依次为：根域名、顶级域名（top level domain，TLD）、二级域名、（三级域名）  
* A (Address) 记录是用来指定主机名（或域名）对应的IP地址记录（域名-ip的映射）  
* CANME记录：别名记录。这种记录允许您将多个名字映射到另外一个域名（域名-别名的映射）。例如，有一台计算机名为“host.mydomain.com”（A记录）。它同时提供WWW和MAIL服务，为了便于用户访问服务。可以为该计算机设置两个别名（CNAME）：WWW和MAIL。这两个别名的全称就 `http://www.mydomain.com/` 和 `http://mail.mydomain.com`。实际上他们都指向 `host.mydomain.com`

#### DNS 查询方式
* 递归查询
  ![DNS递归](.\image\DNS递归.jpg)
* 迭代查询
  ![DNS递归](.\image\DNS迭代.jpg)

#### DNS 解析过程
1. 首先搜索浏览器的 DNS 缓存，缓存中维护一张域名与 IP 地址的对应表；
2. 若没有命中，则继续搜索操作系统的 DNS 缓存（host文件）；
3. 若仍然没有命中，则操作系统将域名发送至本地域名服务器，本地域名服务器查询自己的 DNS 缓存，查找成功则返回结果（注意：主机和本地域名服务器之间的查询方式是递归查询）；
4. 若本地域名服务器的 DNS 缓存没有命中，则本地域名服务器向上级域名服务器进行查询，通过以下方式进行迭代查询（注意：本地域名服务器和其他域名服务器之间的查询方式是迭代查询，防止根域名服务器压力过大）：
5. 本地域名服务器将得到的 IP 地址返回给操作系统，同时自己将 IP 地址缓存起来
6. 操作系统将 IP 地址返回给浏览器，同时自己也将 IP 地址缓存起来
7. 至此，浏览器就得到了域名对应的 IP 地址，并将 IP 地址缓存起来
   ![DNS解析](.\image\DNS解析.jpg)

#### DNS解析优化
* 减少DNS的请求次数：缓存
* 进行DNS预获取：DNS Prefetch（预获取）： 尝试在请求资源之前解析域名
* 使用CDN

### Proxy 跨域原理
在本地运行 npm run dev 等命令时实际上是用 node 运行了一个服务器，因此 proxyTable 实际上是将请求发给自己的服务器，再由服务器转发给后台服务器，做了一层代理。vue的proxyTable用的是http-proxy-middleware中间件, 因此不会出现跨域问题。

### HTTP1.0/1.1/2.0 的区别
#### HTTP 1.0
浏览器与服务器只保持短暂的连接，每次请求都需要与服务器建立一个TCP连接  
如果需要建立长连接，需要设置一个非标准的Connection字段 Connection: keep-alive

#### HTTP 1.1
在HTTP1.1中，默认支持长连接（Connection: keep-alive），即在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟  
HTTP1.1在HTTP1.0的基础上，增加更多的请求头和响应头来完善的功能:
* 引入了更多的缓存控制策略
* 引入range，允许值请求资源某个部分
* 引入host，实现了在一台WEB服务器上可以在同一个IP地址和端口号上使用不同的主机名来创建多个虚拟WEB站点
* 更多错误码

#### HTTP 2.0
* 多路复用: 同时发送和响应多个请求（http2 基于二进制帧传输数据。流是存在于连接中的一个虚拟通道，流可以承载双向消息，同一个TCP中，可以发送多个流）
* 二进制分帧：二进制传帧，速度更快
* 首部压缩：header压缩，降低开销。多个请求首部有相同部分时，请求一发送了所有的头部字段，第二个请求则只需要发送差异数据
* 服务器推送：服务器会顺便把一些客户端需要的资源一起推送到客户端，如在响应一个页面请求中，就可以推送页面的其它资源。免得客户端再次创建连接发送请求到服务器端获取

-----
**参考链接**
* <https://vue3js.cn/interview/http/1.0_1.1_2.0.html#%E4%B8%80%E3%80%81http1-0>

### WebSocket
一种网络传输协议。客户端和服务器只需要完成一次握手，两者之间就可以创建持久性的连接，并进行双向数据传输。  
协议名：引入ws和wss分别代表明文和密文的websocket协议，且默认端口使用80或443，几乎与http一致。

优点：**实时通信**
* 较少的控制开销：数据包头部协议较小，不同于http每次请求需要携带完整的头部
* 更强的实时性：相对于HTTP请求需要等待客户端发起请求服务端才能响应，延迟明显更少
* 保持创连接状态：创建通信后，可省略状态信息，不同于HTTP每次请求需要携带身份验证
* 更好的二进制支持：定义了二进制帧，更好处理二进制内容
* 支持扩展：用户可以扩展websocket协议、实现部分自定义的子协议
* 更好的压缩效果：Websocket在适当的扩展支持下，可以沿用之前内容的上下文，在传递类似的数据时，可以显著地提高压缩率

### 跨站和跨域
#### 跨域
浏览器有同源策略,如果两个URL的协议,域名,端口完全一致才是同源。有一个不一致就是跨域  
同源策略限制
1. DOM: 禁止操作非源页面的DOM与JS对象  
  这里主要场景是iframe跨域的情况，非同源的iframe是限制互相访问的
2. XmlHttpRequest: 禁止使用XHR对象向不同源的服务器地址发起HTTP请求，即不能发送跨域ajax请求
  主要用来防止CSRF（跨站请求伪造）攻击
3. 本地存储: Cookie、LocalStorage 和 IndexDB 无法跨域读取

#### 跨站
Cookie与此息息相关，Cookie实际上遵守的是“同站”策略  
只要两个 URL 的 eTLD+1 相同即是同站,不需要考虑协议和端口
* eTLD: (effective top-level domain) 有效顶级域名，注册于 Mozilla 维护的公共后缀列表（Public Suffix List）中,如.com、.co.uk、.github.io,.top 等
* eTLD+1: 有效顶级域名+二级域名，如 taobao.com,baidu.com,sugarat.top

举几个例子，www.taobao.com 和 www.baidu.com 是跨站，www.a.taobao.com 和 www.b.taobao.com 是同站，a.github.io 和 b.github.io 是跨站(注意是跨站)。

-----
**参考链接**
* <https://juejin.cn/post/6926731819903631368>

### web安全
#### XSS
XSS(Cross-Site Scripting，跨站脚本攻击)是一种代码注入攻击。攻击者在目标网站上注入恶意代码，当被攻击者登陆网站时就会执行这些恶意代码，这些脚本可以读取 cookie，session tokens，或者其它敏感的网站信息，对用户进行钓鱼欺诈，甚至发起蠕虫攻击等  
分类
* 反射型xss：常见于通过 URL 传递参数的功能，如网站搜索、跳转等  
  防范：对url字符串进行编码
* DOM型xss：把不可信的内容插入到了页面。在使用 .innerHTML、.outerHTML、.appendChild、document.write()等API时要特别小心，不要把不可信的数据作为 HTML 插到页面上，尽量使用 .innerText、.textContent、.setAttribute() 等  
  防范：对输入内容进行转义。url使用 encodeURIComponent 来转义；非url转义html、js标签
  ```js
  function encodeHtml(str) {
    return str.replace(/"/g, '&quot;')
            .replace(/'/g, '&apos;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;');
  }
  ```
* 存储型xss：恶意脚本永久存储在目标服务器上.常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等  
  防范：前端传递数据进行转义/过滤；数据库存储数据进行转义/过滤；前端展示数据进行转义/过滤。

#### CSRF
CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的  
典型的CSRF攻击流程：
1. 受害者登录A站点，并保留了登录凭证（Cookie）。
2. 攻击者诱导受害者访问了站点B。
3. 站点B向站点A发送了一个请求，浏览器会默认携带站点A的Cookie信息。
4. 站点A接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是无辜的受害者发送的请求。
5. 站点A以受害者的名义执行了站点B的请求。
6. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者完成了攻击。

![CSRF](.\image\CSRF.png)

防御：
1. 使用验证码--用户体验不好
2. 检查请求来源Referer--Referer可被更改
3. 使用token--主流
4. Samesite Cookie属性

#### 点击劫持
点击劫持是指在一个Web页面中隐藏了一个透明的iframe，用外层假页面诱导用户点击，实际上是在隐藏的frame上触发了点击事件进行一些用户不知情的操作。

-----
**参考链接**
* <https://juejin.cn/post/6844903842635579405>

## Node.js
Node.js 是一个开源与跨平台的 JavaScript 运行时环境

在浏览器外运行 V8 JavaScript 引擎（Google Chrome 的内核），利用事件驱动、非阻塞和异步输入输出模型等技术提高性能

可以理解为 Node.js 就是一个服务器端的、非阻塞式I/O的、事件驱动的JavaScript运行环境

### 优缺点
优点：
* 处理高并发场景性能更佳
* 适合I/O密集型应用，指的是应用在运行极限时，CPU占用率仍然比较低，大部分时间是在做 I/O硬盘内存读写操作

因为Nodejs是单线程，带来的缺点有：
* 不适合CPU密集型应用
* 只支持单核CPU，不能充分利用CPU
* 可靠性低，一旦代码某个环节崩溃，整个系统都崩溃

### node 事件循环（event loop）
![node event loop](.\image\node-eventLoop.png)  
node 中的事件循环的顺序为：
1. 外部输入数据
2. 轮询阶段(poll): 回到 timer 阶段执行回调;执行 I/O 回调
3. 检查阶段(check): 执行 setImmediate() 的回调
4. 关闭事件回调阶段(close callback): 执行 socket 的 close 事件回调
5. 定时器检测阶段(timer): timers 阶段会执行 setTimeout 和 setInterval 回调，并且是由 poll 阶段控制的
6. I/O 事件回调阶段(I/O callbacks): ：处理一些上一轮循环中的少数未执行的 I/O 回调
7. 闲置阶段(idle, prepare): 仅 node 内部使用
8. 轮询阶段（按照该顺序反复运行）

上面六个阶段都不包括 `process.nextTick()`: `process.nextTick`函数其实是独立于 Event Loop 之外的，它有一个自己的队列，当每个阶段完成后，如果存在 nextTick 队列，就会清空队列中的所有回调函数，并且优先于其他 microtask 执行。

### Node.js 全局对象
#### 真正的全局对象
* Class:Buffer： 处理二进制以及非Unicode编码的数据
* process： 进程对象，提供有关当前进程的信息和控制
* console
* clearInterval、setInterval
* clearTimeout、setTimeout
* global： 全局命名空间对象，前面的process、console、setTimeout等都有放到global中
```js
console.log(process === global.process) // true
```

#### 模块级别的全局变量
这些全局对象是模块中的变量，只是每个模块都有，看起来就像全局变量，像在命令交互中是不可以使用，包括
* __dirname: 获取当前文件所在的路径，不包括后面的文件名
```js
// 从 /Users/mjr 运行 node example.js
console.log(__dirname);
// 打印: /Users/mjr
```
* __filename: 获取当前文件所在的路径和文件名称，包括后面的文件名称
```js
// 从 /Users/mjr 运行 node example.js：
console.log(__filename);
// 打印: /Users/mjr/example.js
```
* exports
* module
* requir

### process
* process.env：环境变量，例如通过 `process.env.NODE_ENV 获取不同环境项目配置信息
* process.argv: 在终端通过 Node 执行命令的时候，通过 process.argv 可以获取传入的命令行参数
  * 0: Node 路径（一般用不到，直接忽略）
  * 1: 被执行的 JS 文件路径（一般用不到，直接忽略）
  * 2~n: 真实传入命令的参数
* process.nextTick：这个在谈及 EventLoop 时经常为会提到。与setTimeout的区别：
  * process.nextTick()会在这一次event loop的call stack清空后（下一次event loop开始前）再调用callback
  * setTimeout()是并不知道什么时候call stack清空的，所以何时调用callback函数是不确定的
* process.pid：获取当前进程id
* process.ppid：当前进程对应的父进程
* process.cwd()：获取当前进程工作目录，
* process.platform：获取当前进程运行的操作系统平台
* process.uptime()：当前进程已运行时间，例如：pm2 守护进程的 uptime 值

## 算法与数据结构
### 排序算法
* 冒泡排序：相邻元素交换，每次遍历后，将最大的元素至于最末位。
  ![冒泡排序](.\image\冒泡排序.gif)
* 选择排序：每次遍历后选择最小的元素至新数组中。
  ![选择排序](.\image\选择排序.gif)
* 插入排序：有序数组A,无序数组B，依次将无序数组B中的每一个元素插入有序数组A中合适的位置。
  ![插入排序](.\image\插入排序.gif)
* 归并排序：把数组分成两部分，对两部分的数组递归至只有两个元素时，进行排序，然后合并。合并方式为：两个有序数组A，B，每次比较A,B中小的那个元素，放至新数组C中，A,B中剩余的元素继续比较，直到A,B某个数组元素空了。
  ![归并排序](.\image\归并排序.png)
* 快速排序： 从数组中挑出一个基准数字，按照基准将元素放至基准数左右两边。左右两边的数重复上述操作进行排序。

![算法复杂度](.\image\复杂度.png)

-----
**参考链接**
* <https://vue3js.cn/interview/algorithm/sort.html#%E4%B8%80%E3%80%81%E6%98%AF%E4%BB%80%E4%B9%88>

## 一些手刷题
### 如何实现一个new
```js
function _new(fn, ...arg) {
    const obj = Object.create(fn.prototype); // 继承原型链上的属性
    const ret = fn.apply(obj, arg); // 这里可能return非Object对象
    return ret instanceof Object ? ret : obj;
}

let obj1 =function() {
    return {a: 1};
};
function _new(fn, ...arg) {
    const obj = Object.create(fn.prototype);
    const ret = fn.apply(obj, arg);
    console.log(obj, ret);
    return ret instanceof Object ? ret : obj;
};
let o = _new(obj1); 
o;
// console.log(obj, ret); --> obj1 {} {a: 1}
// {a: 1}
```

### 函数柯里化
>定义  
>红宝书（第3版）：用于创建已经设置好了一个或多个参数的函数。基本方法是使用一个闭包返回一个函数。（P604）  
>维基百科：柯里化（英语：Currying），是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

柯里化技术，主要体现在函数里面返回函数。即将多变量函数拆解为单变量（或部分变量）的多个函数并依次调用
```js
/**
 * @description: 将函数柯里化的工具函数
 * @param {Function} fn 待柯里化的函数
 * @param {array} args 已经接收的参数列表
 * @return {Function}
 */
const currying = function(fn, ...args) {
    // fn需要的参数个数
    const len = fn.length
    // 返回一个函数接收剩余参数
    return function (...params) {
        // 拼接已经接收和新接收的参数列表
        let _args = [...args, ...params]
        // 如果已经接收的参数个数还不够，继续返回一个新函数接收剩余参数
        if (_args.length < len) {
            return currying.call(this, fn, ..._args)
        }
       // 参数全部接收完调用原函数
        return fn.apply(this, _args)
    }
}
```

## ES6
### Proxy 和 Reflect
#### Proxy
```js
let pro = new Proxy(target,handler);
```
* target参数表示所要拦截的目标对象
* handler参数也是一个对象，用来定制拦截行为

作用
* **实现拦截和监视外部对对象的访问**。
* 降低函数和类的复杂度，优雅的写出代理代码。
* 在复杂操作前对操作进行校验或对所需资源进行管理

#### Reflect
**提供了一套统一操作Object的API**  
在实现自定义的get或者set这样的逻辑时更标准的做法是，先去实现自己所需要的监视逻辑，最后再去返回通过Reflect中对应的方法的一个调用结果
·
```js
const p = new Proxy(target, {
  get(obj, property) {
    console.log('监听逻辑');
    return Refelct.get(obj, property);
  }
})
```

----
**参考链接**
* <https://baijiahao.baidu.com/s?id=1685749507237781111&wfr=spider&for=pc>
* <https://zhuanlan.zhihu.com/p/25664314>

## VUE
### vue双向绑定
通过`javascript`控制DOM的展示，就是数据（`Data`）到模板（DOM）的绑定，这是数据单向绑定。  
双向绑定即使在单向绑定的基础上，实现从模板到数据的绑定。  
#### MVVM框架
* 数据层（Model）：应用的数据及业务逻辑，为开发者编写的业务代码；
* 视图层（View）：应用的展示效果，各类UI组件，由 template 和 css 组成的代码；
* 业务逻辑层（ViewModel）：框架封装的核心，它负责将数据与视图关联起来；

**ViewModel的主要职责就是：**
* 数据变化后更新视图
* 视图变化后更新数据

所以viewmodel层主要由两个部分组成：
* **监听器（Observer）**：观察数据，做到时刻清楚数据的任何变化，然后通知视图更新；
* **解析器（Compiler）**：观察UI，做到时刻清楚视图发生的一切交互，然后更新数据；

#### 双向绑定的实现
##### 实现监听器
监听器需要监听数据的变化，并提供通知功能。监听数据变化常见的方式有三种：
* 观察者模式（发布+订阅）；
* 数据劫持；
* 脏检查；

vue采用 观察者模式 + 数据劫持 来实现监听  
**观察者模式**  
在观察者模式中，起主导作用的是发布者。需要手动更新数据，然后通知订阅者  
```js
// 发布者
function Subject() {/
  // 单个发布者的所有订阅者
  this.observers = [];
  
  // 添加一个订阅者
  this.attach = function(callback) {
    this.observers.push(callback);
  };
  
  // 通知所有的订阅者
  this.notify = function(value) {
    this.observers.forEach(callback => callback(value));
  };
}

// 订阅者
function Observer(queue, key, callback) {
  queue[key].attach(callback);
}

// 手动更新数据
function setData(data, key, value) {
    data[key] = value;
    // 通知此值的所有订阅者，数据发生了更新
    messageQueue[key].notify(value);
}

// 消息队列
const messageQueue = {};

// 数据
const myData = { value: "" };

// 将每个数据属性添加可订阅的入口
for (let key in myData) {
  messageQueue[key] = new Subject();
}

// 订阅 value 值的变化
Observer(messageQueue, "value", value => {
  console.warn("value updated:", value);
});

// 更新数据
setData(myData, "value", "hello world.");
setData(myData, "value", 100);
setData(myData, "value", true);
```
**数据劫持**  
`Object.definePropotype`能够定义对象属性的`getter`和`setter`,能够使`setData(myData, 'value', 100)`; 就可以替换成 `myData.value = 100`; 的编写方式。从语法和使用上都变的更简单

##### 实现解析器
* 解析模板中所有的特定特性，例如：v-model、v-text、{{ }}语法等；
* 关联数据展示到DOM；
* 关联事件绑定到DOM；

#### vue3为什么使用proxy代替defineProperty 
vue2中利用`Object.defineProperty`定义了响应式函数`defineReactive`。
* 对一个对象进行删除与添加属性操作，无法劫持到
```js
const obj = {
    foo: "foo",
    bar: "bar"
}
observe(obj)
delete obj.foo // no ok
obj.jar = 'xxx' // no ok
```
* 数组API方法无法监听到
* 需要对每个属性进行遍历监听，如果嵌套对象，需要深层监听，造成性能问题
  + 在对象存在多个key情况下，需要进行遍历
  + 如果存在嵌套对象的情况，还需要在defineReactive中进行递归
  + 当给key赋值为对象的时候，还需要在set属性中进行递归

`Proxy`直接可以劫持整个对象，并返回一个新对象，可以只操作新的对象达到响应式目的  
**Proxy 不兼容IE，也没有 polyfill, defineProperty 能支持到IE9**

-----
**参考链接**
* <https://juejin.cn/post/6844903942254510087>
* <https://vue3js.cn/interview/vue3/proxy.html#%E4%B8%80%E3%80%81object-defineproperty>

### vue-router 钩子
#### 全局导航钩子
* beforeEach(to, from, next): 全局的before 钩子函数,每次每一个路由改变的时候都会执行一遍。可用于页面跳转拦截、鉴权等
* beforeResolve: 同 beforeEach
* afterEach(to, from): 路由跳转之后，没有next，因为不可以改变导航

#### 路由内钩子
```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

#### 组件内钩子
* beforeRouteEnter： 渲染该组件的路由被confirm前调用，不能使用this
* beforeRouteUpdate： 路由改变但是当前组件被复用时调用
* beforeRouteLeave： 离开该组件时调用

## H5 新特性
* 语义化标签，例如header，footer，section，article等 语义化标签的作用：提升页面的阅读性(结构性增强)，更有利于SEO
* 新增媒体元素，audio、video audio和video标签能够很容易的输出音频或视频流，提供便利的获取文件信息的API
* 用于绘画的canvas属性
* 新增本地存储方式：sessionStorage、localStorage
* 新的技术：webworker、websocket 
  * webworker：用于多线程编程
  * websocket：客户端与服务端双向数据通信协议
* 新增的表单控件：calendar、date、time、email、url、search

## ES6 新特性
* let、const、block作用域
* 函数：
  * 箭头函数
  * 函数参数默认值
  * rest参数
* 解构赋值，拓展运算符
* 模板字符串
* 数组拓展： 
  * Array.from: 将类数组转化为数组
  * find()findIndex()
  * includes()
* 对象拓展
  * 属性简写
  * 属性名可使用表达式
* Symbol
* Set, Map
* Promise,async await
* Class
* Module:在ES6的module之前，比较流行的模块加载方案有:CommonJS和AMD，前者用于服务器（node)，后者用于浏览器。区别：
  * CommondJS和AMD是运行时加载的。
  * module是编译时加载的。
  * CommondJS输出的是值的复制，而ES6输出的是值的引用

### SSR
SSR是Server Side Rendering的缩写，它可以将组件渲染成为HTML字符串，发送到浏览器，最后将这些静态标记"激活"为客户端上完全可交互的应用程序，它的出现是为了解决SPA问题  

优点：
* 利于SEO，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面。
* 更快的内容到达时间，页面渲染的更快

#### vue的服务端渲染
服务端渲染的Vue.js的应用程序，使Vue既可以在服务端运行，也可以在客户端（浏览器）运行.
![vue ssr](.\image\vue的ssr.jpg)
将 Source（源码）通过 webpack 打包出两个 bundle，其中 Server Bundle 是给服务端用的，服务端通过渲染器 bundleRenderer 将 bundle 生成 html 给浏览器用；另一个 Client Bundle 是给浏览器用的，别忘了服务端只是生成前期首屏页面所需的 html ，后期的交互和数据处理还是需要能支持浏览器脚本的 Client Bundle 来完成。  

服务端只有beforeCreate和created两个生命周期，因为SSR只会生成HTML字符串，不会渲染DOM结构，也就没有beforeMount和mounted,也就不会进行更新，所以也不会有beforeUpdate和updated。  

**SSR渲染路由能用history模式**：hash模式不会将数据提交给服务器，所以必须使用hisory模式

-----
**参考链接**
* <https://juejin.cn/post/6965748513674100743>
