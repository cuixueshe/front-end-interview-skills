## 事件循环机制相关
样例代码：
``` javascript
console.log(1)

const promise = new Promise(function(resolve => {
  console.log(2)
  resolve()
  console.log(3)
})

async function foo() {
  console.log(4)
  await promise
  console.log(6)
}

foo()

promise.then(() => {
  console.log(7)
})

setTimeout(() => console.log(8))
console.log(5)
```

### 1.宏任务、微任务先后执行顺序，Promise 以及 async 中哪些部分是同步，哪些部分是异步？


### 2.还有哪些时序相关的函数？
setInterval
浏览器端：requestAnimationFrame、requestIdleCallback
Node 端：setImmediate、nextTick

分别有什么区别？如何实现 Polyfill？


### 3.如何实现 Promise；async 函数编译成 ES5 代码是怎样的？ Vue 的 nextTick 如何实现的



### 4.Node 的事件循环流程；是否了解过 queueMicritask



## 描述一下 HTTP 缓存策略？
### 1.强缓存、协商缓存；HTTP 常用的头部、状态码，基本的流程

### 2.HTTP、HTTPS、HTTP2的区别；网络模型，TCP 三次握手，四次挥手

### 3.非对称加密，证书，Hash，常用的加密算法，HTTPS 握手过程；多路复用

### 4.DNS、CDN、Websocket；网络安全，中间人攻击、CDRF、XSS、SQL注入，同形异义字；URL 编码


## 请谈一下你对 React 的认识？
### 1.生命周期，新旧生命周期的区别？无状态组件、 PureComponent、高阶组件（HOC）[基础实践]

### 2.Hooks，Provider/Context，Ref，Protal，错误处理，Redux 及其中间件 [高级实践]

### 3.JSX原理，虚拟 DOM 原理，setState 同步异步，浅比较 [框架原理]


### 4.合成时间，Fiber、FiberNode、workInProgress；SSR、React Native；性能优化：耗时、帧率、CPU、内存泄漏、回流重绘 [相关领域知识]

## Vue 的 nextTick 实现的时候为什么使用微任务不使用宏任务
在 vue2.5 源码的 `src/core/util/next-tick.js` 中有注释提及，大意如下：

在 2.5 中，我们使用了（宏）任务（结合微任务）。但是，在重绘之前更改状态时会出现一些微妙的问题（例如#6813，out-in 转换）。此外，在事件处理程序中使用（宏）任务会导致一些奇怪的行为无法规避的（例如#7109、#7153、#7546、#7834、#8109。所以我们现在再次在任何地方使用微任务。这种权衡的一个主要缺点是存在一些场景微任务的优先级太高，据说在两者之间触发顺序事件（例如#4521、#6690，它们有解决方法）甚至在同一事件的冒泡之间（#6566）。

所以 vue 目前是优先采用 `Promise.then` 或 `MutationObserver`，如果本机不支持则使用 `setImmediate`，从技术上讲，它利用（宏）任务队列，但它仍然是比 `setTimeout`` 更好的选择，如果本机不支持则使用 `setTimeout` 做最后兜底。

## 阅读一下代码说出输出结果
```javascript
async function async1() {
  console.log('async1 start')
  await async2()
  console.log('async1 end')
  return 'async return'
}
async function async2() {
  console.log('async2')
}
console.log('script start')
setTimeout(function() {
  console.log('setTimeout')
}, 0)
async1().then(function(message) {
  console.log(message)
})
new Promise(function(resolve) {
  console.log('promise1')
  resolve()
  }).then(function() {
  console.log('promise2')
})
console.log('script end')
```

参考答案：
```
/**
 * 1 script start // 开始执行 console    并向  宏任务队列push setTimeout
 * 2 async1 start // 执行async1 --> 执行 console
 * 3 async2     // 执行async1 输出  并移交控制权到父执行栈 并清空同步任务
 * 4 promise1 // 执行 构造函数  并向 微任务队列 push 回调1
 * 5 script end  // 同步代码执行结束 控制权回到 async1
 * 6 async1 end  // 执行console 并向 微任务队列 push 回调2
 * 7 promise2   // 开始清空 微任务队列 执行回调1
 * 8 async return  // 开始清空 微任务队列 执行回调2
 * 9 setTimeout // 开始清空 宏任务队列
 */
```
## Vue 的 nextTick 为什么能在微任务中操作到最新的 Dom
我们通常认为只有在页面渲染后才能拿到最新的 DOM 并对其进行操作，这是不够严谨的。因为 DOM Tree 的修改是同步的，只有渲染过程是异步的，也即是我们在操作 DOM 之后，可以立即获取到最新的 DOM 信息，但此信息不会同步地渲染到界面上

如何证明这一点呢，大家可以尝试去运行一下下面的例子
```html
<body>
  <div id="app">旧的不去</div>
  <script>
      app.innerText = '新的不来'
      Promise.resolve().then(() => {
        console.log('微任务执行')
      })
      // 此处渲染将被阻塞，同时打印的是「新的不来」当用户确认 alert 弹窗之后，会去执行微任务
      alert(`渲染阻塞, ${document.getElementById('app').innerText}`)
  </script>
</body>
```

那么，比微任务快就一定是同步任务吗？显然不是的，比如 node 环境中的 `process.nextTick` 执行就比微任务快，上述的证明只体现了 DOM Tree 的修改是比微任务快的，这样 Vue 将 nextTick 设计成微任务也就可以理解了，它完全可以在 nextTick 中拿到最新的 DOM Tree，而我们最终通过浏览器渲染看到具体改变效果也将远在这些操作之后，所以，在使用 Vue 的时候，我们会通过 nextTick 去确保获取的是最新的 DOM 结构，尽管它是一个微任务，浏览器此时也没有渲染出最新的 DOM，但由于 DOM Tree 的修改是同步的，或者说经过上述证明是比微任务快的，我们就可以放心的使用它

## 你了解浏览器是如何渲染一个页面的吗
整个渲染过程比较的复杂，要经历如下步骤：
- 数据解析阶段
  - 将 HTML 文本 构建为 DOM 树
  - 将样式文本通过渲染引擎转换为浏览器可以理解的 styleSheet
  - 将 styleSheet 标准化
  - 计算出 DOM 树上的每个节点对应的样式，这里涉及到 css 的继承规则和层叠规则，可以继续问面试者
- 布局阶段
  - 创建布局树，DOM 树是全量信息，也包含 head 或者类似 display: none 这种不可见的元素，为了渲染需要在构建一颗值包含可见元素的元素布局树
  - 布局计算，有了布局树和样式表下一步就是要计算出布局树中每个节点的坐标位置