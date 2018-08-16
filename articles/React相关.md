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



