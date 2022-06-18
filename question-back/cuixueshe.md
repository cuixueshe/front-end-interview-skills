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
