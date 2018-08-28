### Node相关



#### koa2中使用到的一些中间件

路由：koa-router

处理请求：koa-bodyparser

静态资源：koa-static-cache

sesion：koa-session



#### koa2是如何管理中间件的

Koa 中间件是以级联代码（Cascading） 的方式来执行的，是一个洋葱模型。

调用 `use(fn)` 方法中的参数（不管是普通的函数或者是中间件）都添加到 `this.middlware` 这个数组中。

通过 `compose()` 这个方法，就能将我们传入的中间件数组转换并级联执行，最后 `callback()` 返回`this.handleRequest()`的执行结果

```js
 // compose()
 return function (context, next) {
    // 记录上一次执行中间件的位置 
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      // 理论上 i 会大于 index，因为每次执行一次都会把 i递增，
      // 如果相等或者小于，则说明next()执行了多次
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      // 取到当前的中间件
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, function next () {
          return dispatch(i + 1)
        }))
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```

执行流程大致如下：

1. 先执行第一个中间件（因为`compose` 会默认执行 `dispatch(0)`），该中间件返回 `Promise`，然后被 `Koa` 监听，执行对应的逻辑（成功或失败）
2. 在执行第一个中间件的逻辑时，遇到 `await next()`时，会继续执行 `dispatch(i+1)`，也就是执行 `dispatch(1)`，会手动触发执行第二个中间件。这时候，第一个中间件 `await next()` 后面的代码就会被 pending，等待 `await next()` 返回 `Promise`，才会继续执行第一个中间件 `await next()` 后面的代码。
3. 同样的在执行第二个中间件的时候，遇到 `await next()` 的时候，会手动执行第三个中间件，`await next()` 后面的代码依然被 pending，等待 `await` 下一个中间件的 `Promise.resolve`。只有在接收到第三个中间件的 `resolve`后才会执行后面的代码，然后第二个中间件会返回 `Promise`，被第一个中间件的 `await` 捕获，这时候才会执行第一个中间件的后续代码，然后再返回 `Promise`
4. 以此类推，如果有多个中间件的时候，会依照上面的逻辑不断执行，先执行第一个中间件，在 `await next()` 出 pending，继续执行第二个中间件，继续在 `await next()` 出 pending，继续执行第三个中间，直到最后一个中间件执行完，然后返回 Promise，然后倒数第二个中间件才执行后续的代码并返回Promise，然后是倒数第三个中间件，接着一直以这种方式执行直到第一个中间件执行完，并返回 `Promise`，从而实现文章开头那张图的执行顺序。

通过上面的分析之后，如果你要写一个 koa2 的中间件，那么基本格式应该就长下面这样：

```js
async function koaMiddleware(ctx, next){
    try{
        // do something
        await next()
        // do something
    }
    .catch(err){
        // handle err
    }    
}
```



> https://segmentfault.com/a/1190000012881491





#### Koa和Express的区别

`koa`的确是比`express`轻量的多，`koa`给我的感觉更像是一个中间件框架，`koa`只是一个基础的架子，需要用到的相应的功能时，用相应的中间件来实现就好，诸如路由系统等。一个更好的点在于，`express`是基于回调来处理，至于回调到底有多么的不好，大家可以自行搜索来看。`koa1`基于的`co`库，所以`koa1`利用`Generator`来代替回调，而`koa2`由于`node`对`async/await`的支持，所以`koa2`利用的是`async/await`。



