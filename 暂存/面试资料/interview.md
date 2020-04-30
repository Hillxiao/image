### 常规题

- **判断⼀个变量的正确类型**

  ```js
  // 避免 typeof xxx 判断 null 的 bug
  Object.prototype.toString.call(xxx) // "[object Null]"
  ```

- **new**
  - 新⽣成了⼀个对象
  - 链接到原型 
  - 绑定 this
  - 返回新对象

- **对象拷贝**

  - 浅拷贝：

    - Object.assign({}, a)
    - {...a}

  - 深拷贝：

    - JSON.parse(JSON.stringify(object))

      ```
      // 有局限性
      会忽略 undefined
      不能序列化函数
      不能解决循环引⽤的对象
      ```

    - Lodash.js 深拷⻉

- **模块化**

  - Babel(ES6)

    ```js
    // file a.js
    export function a() {}
    export function b() {}
    // file b.js
    export default function() {}
    
    import {a, b} from './a.js'
    import XXX from './b.js'
    ```

  - CommonJS(Node)

    ```js
    // a.js
    module.exports = {
        a: 1 
    }
    // or
    exports.a = 1
    // b.js
    var module = require('./a.js')
    module.a // -> log 1
    ```

  - AMD(RequireJS )

    ```js
    define(['./a', './b'], function(a, b) {
        a.do()
        b.do() 
    })
    define(function(require, exports, module) { 
        var a = require('./a') 
        a.doSomething() 
        
        var b = require('./b')
        b.doSomething() 
    })
    ```

- **防抖和节流**

  > 防抖和节流的作⽤都是防⽌函数多次调⽤。区别在于，假设⼀个⽤户⼀直触发这个函数，
  >
  > 且每次触发函数的间隔⼩于 wait ，防抖的情况下只会调⽤⼀次，
  >
  > ⽽节流的情况会每隔⼀定时间（参数 wait ）调⽤函数

- **继承**

  - ES5——组合继承

    ```JS
    function Super() {}
    Super.prototype.getNumber = function() {
        return 1 
    }
    function Sub(sex) {
        // Super.call(this, arguments)
        this.sex = sex;
    }
    Sub.prototype = new Super();
    Sub.prototype.constructor = Sub;
    let s = new Sub('男');
    console.log(s.sex);
    s.getNumber();
    ```

  - ES6——class extends

    ```js
    // 编译过的代码调⽤ myDate.test() 会出错，由于在 JS 底层有限制，如果不是由 Date 构造出来的实例的话，是不能调 ⽤ Date ⾥的函数的。
    class MyDate extends Date {
        test() {
            return this.getTime()
        } 
    }
    let myDate = new MyDate()
    myDate.test()
    ```

    ```js
    // 先创建⽗类实例 => 改变实例原先的 _proto__ 转⽽连接到⼦类的
    prototype => ⼦类的 prototype 的 __proto__ 改为⽗类的 prototype
    function MyDate() {}
    MyDate.prototype.test = function() {
        return this.getTime()
    }
    let d = new Date()
    Object.setPrototypeOf(d, MyDate.prototype)
    Object.setPrototypeOf(MyDate.prototype, Date.prototype)
    d.test()
    ```

- **Promise**——异步编 程

-  **Event loop**

  > - ⾮阻塞单线程语⾔，同步
  >
  > - JS 在执⾏的过程中会产⽣执⾏环境，这些执⾏环境会被顺序的加⼊到执⾏栈中。如果遇到异步的代码，会被挂起并加⼊到 Task （有多种 task ） 队列中。⼀旦执⾏栈为空，Event Loop 就会从 Task 队列中拿出需要执⾏的代码并放⼊执⾏栈中执⾏，所以本质上来说 JS 中的异步还是同步⾏为
  >
  > - 不同的任务源会被分配到不同的 Task 队列中，任务源可以分为 微任务（ microtask ） 和 宏任务（ macrotask ）。在 ES6 规范中，microtask 称为 jobs，macrotask 称为 task
  >
  > - Promise 属于微任务⽽ setTimeout 属于宏任务

  - **微任务**
    - process.nextTick
    - promise
    - Object.observe
    - MutationObserver

  - **宏任务**
    - script
    - setTimeout
    - setInterval
    - setImmediate
    - I/O
    - UI rendering

  > - 执⾏同步代码，这属于宏任务 
  >
  > - 执⾏栈为空，查询是否有微任务需要执⾏ 
  >
  > - 执⾏所有微任务 
  >
  > - 必要的话渲染 UI 
  >
  > - 然后开始下⼀轮 Event loop ，执⾏宏任务中的异步代码

- **重绘（Repaint）和回流（Reflow）**

  > 在构建 CSSOM 树时，会阻塞渲染，直⾄ CSSOM 树构建完成
  >
  > 重绘是当节点需要更改外观⽽不会影响布局的，⽐如改变 color 就叫称为重绘 
  >
  > 回流是布局或者⼏何属性需要改变就称为回流

  - 重绘和回流其实和 **Event loop** 有关

  - 减少重绘和回流 

    ```
    使⽤ translate 替代 top
    使⽤ visibility 替换 display: none ，因为前者只会引起重绘，后者会引发回流 （改变了布局） 不要使⽤ table 布局，可能很⼩的⼀个⼩改动会造成整个 table 的重新布局
    动画实现的速度的选择，动画速度越快，回流次数越多，也可以选择使⽤
    requestAnimationFrame
    CSS 选择符从右往左匹配查找，避免 DOM 深度过深 将频繁运⾏的动画变为图层，图层能够阻⽌该节点回流影响别的元素。⽐如对于 video
    标签，浏览器会⾃动将该节点变为图层
    ```

- **性能优化**
  - 懒加载
  - 图⽚优化
    - 压缩图片
    - 雪碧图
  - script 标签放在 body 底部
  - CDN 加载

- 安全
  - XSS(跨⽹站指令码)：通过修改 HTML 节点或者执⾏ JS 代码来攻击⽹站——例如通过 URL 获取某些参数
  - CSRF(跨站请求伪造)：利⽤⽤户的登录态发起恶意请求
  - 密码安全：不同加密算法进行加密



### 小程序

#### 登录

- unionid 和 openid
  - OpenId 是⼀个⽤户对于⼀个⼩程序／公众号的标识，开发者可以通过这个标识识别出⽤户。
  - UnionId 是⼀个⽤户对于同主体微信⼩程序／公众号／ APP 的标识，开发者需要在微信开放平台下绑定相同账号的主体。开发者可通过 UnionId ，实现多个⼩程序、公众号、甚⾄APP 之间的数据互通了。

- 关键Api
  - wx.login 官⽅提供的登录能⼒
  - wx.checkSession 校验⽤户当前的 session_key 是否有效
  - wx.authorize 提前向⽤户发起授权请求
  - wx.getUserInfo 获取⽤户基本信息 

- 步骤：
  - ⼩程序客户端通过 wx.login 获取 code
  - 传递 code 向服务端，服务端拿到 code 调⽤微信登录凭证校验接⼝，微信服务器返回 openid 和会话钥 session_key ，此时开发者服务端便可以利⽤ openid ⽣成⽤户⼊库，再向⼩程序客户端返回⾃定义登录态
  - ⼩程序客户端缓存 （通过 storage ）⾃定义登录态（ token ），后续调⽤接⼝时携带该登录态作为⽤户身份标识即可

#### 存在问题

- ⼩程序仍然使⽤ WebView 渲染，并⾮原⽣渲染。（部分原⽣）
- 不能使⽤ npm
- ⼩程序⼤⼩限制 2M，分包总计不超过 8M
- 服务器域名每个月只能修改五次，无法添加































