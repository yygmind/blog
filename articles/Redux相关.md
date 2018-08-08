### Redux相关



Flux 作为 Facebook 发布的一种应用架构，他本身是一种模式，而不是一种框架，基于这个应用架构模式，在开源社区上产生了众多框架，其中最受欢迎的就是Redux。

Flux 需要四大部分组成：Dispatcher、Stores、Views/Controller-Views、Actions，其中的 Views/Controller-Views 可以理解为React 组件，其作用是从 state 当中获取到相应的数据，并将其传递给他的子组件（descendants）。而另外 3 个部分，则是由 Redux 来提供了。

```js
import {createStore} from 'redux'

function reducer(state = {}, action) {
  return action;
}

let store = createStore(reducer);
```



将一个名为 `reducer` 的函数作为参数，生成我们所需要的 store，reducer 接受两个参数，一个是存储在 store 里面的 state，另一个是每一次调用 dispatch 所传进来的 action。reducer 的作用，就是对 dispatch 传进来的 action 进行处理，并将结果返回。而里面的 state 可以通过 store 里面的 getState 方法进行获得，其结果与最后一次通过 reducer 处理后的结果保持一致。



通过单例保存一个多组件共享的数据对象。？？？哪里有



可以通过以下方式对Redux进行封装





 