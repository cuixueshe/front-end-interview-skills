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