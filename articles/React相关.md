### React相关



#### 生命周期



![](https://user-gold-cdn.xitu.io/2018/8/12/1652a030ed1506e0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

##### 挂载阶段：

- constructor
- getDerivedStateFromProps
- ~~componentWillMount/UNSAVE_componentWillMount~~
- render
- componentDidMount



##### 更新阶段：

* ~~componentWillReceiveProps/UNSAFE_componentWillReceiveProps~~
* getDerivedStateFromProps
* shouldComponentUpdate
* ~~componentWillUpdate/UNSAFE_componentWillUpdate~~
* render
* getSnapshotBeforeUpdate
* componentDidUpdate



##### 卸载阶段

* componentWillUnmount



#### setState

要修改state，只能使用this.setState()，不能使用this.state.value=2类似方式设置state，一是不会驱动重新渲染，二是很可能被后面的操作替换，造成无法预知的错误。此外，React利用状态队列来实现setState的异步更新，避免频繁地重复更新state。



#### React中的key为什么不可以用index

数组中间插入一个值以后，后面就无法保持原来的 key 值，导致需要重新渲染，浪费了性能。建议使用`item.id`。



#### React事件机制

React采用的是**顶层事件代理机制**，能够保持事件冒泡的一致性。React 实现了一个“合成事件”层，这个事件层消除了 IE 与 W3C 标准实现之间的兼容问题。首先区分原生事件与合成事件，我们在 componentDidMount 方法里面通过 addEventListener 绑定的事件就是浏览器原生事件，使用原生事件的时候注意在 componentWillUnmount 解除绑定 removeEventListener。所有通过 JSX 这种方式绑定的事件都是绑定到“合成事件”。 “合成事件”会以事件委托（event delegation）的方式绑定到组件最上层，并且在组件卸载（unmount）的时候自动销毁绑定的事件。 





#### React-Router路由管理原理

1、最开始的网页是多页面的，后来出现了 Ajax 之后，才慢慢有了 SPA。但是用户在使用的过程中，url 不会发生任何改变。当用户操作了几步之后，一不小心刷新了页面，又会回到最开始的状态。可以**通过 hash 的改变来对页面进行局部刷新，hash 的改变会触发 onhashchange 事件**。

但是url 上会有一个 # 号，很不美观。解决方案是：**抛弃 hash，使用 history**



2、通过`history.pushState`或者`history.replaceState`，也能做到：**改变 url 的同时，不会刷新页面**。所以 history 也具备实现路由控制的潜力。然而，还缺一点：**hash 的改变会触发 onhashchange 事件，history 的改变会触发什么事件呢**？ → **很遗憾，没有**。
怎么办呢？→ 虽然我们无法监听到 history 的改变事件，然而，**如果我们能罗列出所有可能改变 history 的途径，然后在这些途径一一进行拦截，不也一样相当于监听了 history 的改变吗**？
对于一个应用而言，url 的改变只能由以下 3 种途径引起：

* 1、点击浏览器的前进或者后退按钮；

* 2、点击 a 标签；

* 3、在 JS 代码中直接修改路由

第 2 和第 3 种途径可以看成是一种，因为 a 标签的默认事件可以被禁止，进而调用 JS 方法。关键是第 1 种，HTML5 规范中新增了一个 [onpopstate](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/onpopstate) 事件，通过它便可以监听到前进或者后退按钮的点击。
要特别注意的是：调用`history.pushState`和`history.replaceState`并不会触发 onpopstate 事件。

总结：经过上面的分析，**history 是可以用来进行路由控制的，只不过需要从 3 方面进行着手**。

```js
history.go(-1);       // 后退一页
history.go(2);        // 前进两页
history.forward();     // 前进一页
history.back();      // 后退一页

// HTML5新增
history.pushState();         // 添加新的状态到历史状态栈
history.replaceState();     // 用新的状态代替当前状态
history.state             // 返回当前状态对象
```



3、React-Router v4 所谓的局部刷新，其本质是：**三个 comppnent 一直都在。当路由发生变化时，跟当前 url 匹配的 component 正常渲染；跟当前 url 不匹配的 component 渲染为 null，仅此而已**，这其实跟 jQuery 时代的 show 和 hide 是一样的道理。

思路分析

![](https://user-images.githubusercontent.com/8401872/29739490-c1dbb054-8a71-11e7-9c9f-31cbbd6adbcb.png)



> https://github.com/youngwind/blog/issues/109





#### React怎么对数据进行更新



#### React组件更新原理



#### React高阶组件

**高阶函数**的定义：接收函数作为输入，或者输出另一个函数的一类函数，被称作高阶函数。对于**高阶组件**，它描述的便是接受React组件作为输入，输出一个新的React组件的组件。

更通俗地描述为，**高阶组件**通过包裹（wrapped）被传入的React组件，经过一系列处理，最终返回一个相对增强（enhanced）的React组件，供其他组件调用。