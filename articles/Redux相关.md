### Redux相关



```js
import {createStore} from 'redux'

function reducer(state = {}, action) {
  return action;
}

let store = createStore(reducer);
```



将一个名为 `reducer` 的函数作为参数，生成我们所需要的 store，reducer 接受两个参数，一个是存储在 store 里面的 state，另一个是每一次调用 dispatch 所传进来的 action。reducer 的作用，就是对 dispatch 传进来的 action 进行处理，并将结果返回。而里面的 state 可以通过 store 里面的 getState 方法进行获得，其结果与最后一次通过 reducer 处理后的结果保持一致。





 