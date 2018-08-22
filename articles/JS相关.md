### JS相关



#### 介绍Promise，以及优缺点？

ES6中原生实现了Promise对象，通过状态传递异步操作的消息。Promise对象有三种状态：**pending(进行中)、fulfilled(已完成)、rejected(已失败)**，根据异步操作的结果决定处于何种状态。一旦状态改变就不可再变，状态改变仅有两种pending=>rejected、pending=>resolved。 

优点：避免了层层嵌套的回调函数，并提供了统一的接口，使异步操作更加容易。

缺点：无法取消Promise；如果不设置回调函数，内部错误无法反映到外部。



多个异步请求在一起，会发生回调地狱的问题，带来阅读和维护困难。

promise 本质上是分离了异步数据获取和业务逻辑，从而让开发人员能专注于一个事物，而不必同时考虑业务和数据。

```js
// 第一部分 数据获取和加工阶段
let getUserName = () => {
    return new Promise(function (resolve, reject) {
        $.get('xxx.com/getUserName', data => resolve(data));
    };
}
let getMobile = userName => {
    return new Promise(function (resolve, reject) {
        $.get('xxx.com/getUserMobile?user=' + userName, data => resolve(data));
    });
}

// 第二部分 业务逻辑部分
getUserName().then(userName => {
    return getMobile(userName);
}).then(mobile => {
    console.log(mobile);
});
```





#### 使用Promise 写一个Ajax处理过程

```js
let http = {
    get: url => {
        let promise = new Promise((resolve, reject) => {
            $.ajax({
                url: url,
                method: 'get',
                success: data => resolve(data),
                error: (xhr, statusText) => reject(statusText)
            });
        });
        return promise;
    }
};

http.get('/login').then(data => {
    return data;
}, err => {
    return Promise.reject('Sorry, file not Found.');
}).then(data => {
    document.write(data);
}, err => {
    document.write(err);
});
```





####  Async和callback的区别

