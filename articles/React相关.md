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



React 自己实现了一套高效的事件注册、存储、分发和重用的逻辑，在 DOM 事件体系基础上做了很大改进。不仅减少了内存消耗，最大化解决了 IE 等浏览器的不兼容问题，而且简化了事件逻辑，对开发者来说非常友好。它有如下的特点：

- 使用**事件委托**技术进行事件代理，React 组件上声明的事件最终都转化为 DOM 原生事件，绑定到了 document 这个 DOM 节点上。从而减少了内存开销。
- 自身实现了一套**事件冒泡机制**，以队列形式，从触发事件的组件向父组件回溯，调用在 JSX 中绑定的 callback。因此我们也没法用 event.stopPropagation() 来停止事件传播，应该使用 React 定义的 event.preventDefault()。
- React 有一套自己的**合成事件** SyntheticEvent，而不是单纯的使用 DOM 原生事件，但二者可以平滑转化。
- React 使用**对象池**来管理合成事件对象的创建和销毁，这样减少了垃圾的生成和新对象内存的分配，大大提高了性能。



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



#### state状态更改时，组件发生了变化，如何知道要去渲染新的值，有个生命周期有两个参数（next, this）



#### React组件更新原理



#### React高阶组件

**高阶函数**的定义：接收函数作为输入，或者输出另一个函数的一类函数，被称作高阶函数。对于**高阶组件**，它描述的便是接受React组件作为输入，输出一个新的React组件的组件。

更通俗地描述为，**高阶组件**通过包裹（wrapped）被传入的React组件，经过一系列处理，最终返回一个相对增强（enhanced）的React组件，供其他组件调用。

 

#### rn写的组件如何变成原生的

![](http://upload-images.jianshu.io/upload_images/1498391-a146678013f455eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> https://my.oschina.net/u/1181284/blog/1553147



#### shouldComponentUpdate怎么判断对象

引用类型数据一直返回true，那就得想办法处理，能不能把前后的数据变成不一样的引用呢，那样不就不相等了吗？于是就有了我们的不可变数据。react官方提供了一个[Immutability Helpers](https://facebook.github.io/react/docs/update.html)

> http://imweb.io/topic/577512fe732b4107576230b9