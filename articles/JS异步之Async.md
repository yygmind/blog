



**我们都知道Promise是一个立即执行函数，但是他的成功（或失败：reject）的回调函数resolve却是一个异步执行的回调。** **当执行到resolve()时，这个任务会被放入到回调队列中，等待调用栈有空闲时事件循环再来取走它。**



然后执行到 await async2()，发现 async2 也是个 async 定义的函数，所以直接执行了“console.log('async2')”，同时async2返回了一个Promise，划重点：**此时返回的Promise会被放入到回调队列中等待，await会让出线程（js是单线程还用我介绍吗），接下来就会跳出 async1函数 继续往下执行。**

 

 

 

 ```Js
async function async1(){
   console.log('async1 start');
    await async2();
    console.log('async1 end')
}
async function async2(){
    console.log('async2')
}
console.log('script start');
setTimeout(function(){
    console.log('setTimeout')
},0);
async1();
new Promise(function(resolve){
    console.log('promise1');
    resolve();
}).then(function(){
    console.log('promise2')
});
console.log('script end')
 ```

