### JS相关



#### 介绍Promise，以及优缺点？

ES6中原生实现了Promise对象，通过状态传递异步操作的消息。Promise对象有三种状态：**pending(进行中)、fulfilled(已完成)、rejected(已失败)**，根据异步操作的结果决定处于何种状态。一旦状态改变就不可再变，状态改变仅有两种pending=>rejected、pending=>resolved。 



**优点：**

使用链式调用的方式来组织代码，将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。

**缺点：**

* 1、无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。

* 2、如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。

* 3、当处于`Pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。



`Promise` 本质上是分离了异步操作和业务逻辑，从而让开发人员能专注于一个事物，而不必同时考虑业务和数据。

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



错误处理：

1、`promise.then` 方法接收两个回调函数为参数，其中第一个为成功回调函数，第二个为失败回调函数，这两个回调函数是基于`promsie`这个 实例的处理结果。`then` 里的错误处理函数是对上一个处理过程的错误捕捉。

```js
Promise.resolve('a:1')
  .then(JSON.parse)
  .then((json) => {
    console.log(json)
  }, (err) => {
    //这儿捕获的是上一个JSON.parse时的错误
    console.log(err.message) //Unexpected token a
  })
```

2、`catch` 很方便的解决了多级 `Promise` 处理错误的问题。

```js
Promise.resolve('{"a":1}')
  .then(JSON.parse)
  .then((json) => {
    return Promise.resolve(json.a++)
  })
  .then((a) => {
    return Promise.reject(new Error('error 1'))
  })
  .then(() => {
    return Promise.reject(new Error('error 2'))
  })
  .catch((err) => {
    console.log(err.message) //error 1
  })
```

3、对于一些忘记加或者无法加 `catch` 的情况可以使用一个全局的事件。node叫 `unhandledRejection`，浏览器是全小写`unhandledrejection`.

```js
const onUnhandledRejection = (function () {
  const isNode = typeof process === 'object' &&
    Object.prototype.toString.call(process) === '[object process]'

  if (isNode) {
    return function (cb) {
      process.on('unhandledRejection', cb)
    }
  }

  return function (cb) {
    self.addEventListener('unhandledrejection', (ev) => {
      cb(ev.reason, ev.promise)
    })
  }
})()

/* 使用：
onUnhandledRejection((r,p) => {
  console.log(r)
})
*/
```

> http://jser.me/2016/04/24/Promise%E7%9A%84%E9%94%99%E8%AF%AF%E5%A4%84%E7%90%86.html



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



#### JS中的堆内存和栈内存

JavaScript中的变量分为基本类型和引用类型。基本类型就是保存在栈内存中的简单数据段，而引用类型指的是那些保存在堆内存中的对象。

1、基本类型

基本类型有undefined、null、boolean、number、string和symbol。这些类型在内存中分别占有固定大小的空间，他们的值保存在栈空间，我们通过按值来访问。

2、引用类型

引用类型，值大小不固定，栈内存中存放地址指向堆内存中的对象。是按引用访问的。如下图所示：栈内存中存放的只是该对象的访问地址，在堆内存中为这个值分配空间。由于这种值的大小不固定，因此不能把它们保存到栈内存中。但内存地址大小的固定的，因此可以将内存地址保存在栈内存中。 这样，当查询引用类型的变量时， 先从栈中读取内存地址， 然后再通过地址找到堆中的值。对于这种，我们把它叫做按引用访问。

![](https://img-blog.csdn.net/20141212220233511?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGRkMTk5MTA1MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

```js
let a = {name: "web前端开发"};
let b = a;
a = null; // 改变的只是栈内存中存放的地址

b; // {name: "web前端开发"} 

```





#### 观察者模式与发布/订阅模式区别

![](https://user-gold-cdn.xitu.io/2017/11/22/15fe1b1f174cd376?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

很多人把观察者模式和订阅模式混淆一谈，其实订阅模式有一个调度中心，对订阅事件进行统一管理。而观察者模式可以随意注册事件，调用事件，虽然实现原理都雷同，设计模式上有一定的差别，实际代码运用中差别在于：订阅模式中，可以抽离出调度中心单独成一个文件，可以对一系列的订阅事件进行统一管理。这样和观察者模式中的事件漫天飞就有千差万别了，在开发大型项目的时候，订阅/发布模式会让业务更清晰！



> http://www.cnblogs.com/lovesong/p/5272752.html
>
> https://juejin.im/post/5a14e9edf265da4312808d86



#### this介绍

`this`实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

当一个函数被调用时，会创建一个活动记录（执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方式、传入的参数等信息。**this就是这个记录的一个属性**，会在函数执行的过程中用到。

* 1、默认绑定

  - **独立函数调用**，可以把默认绑定看作是无法应用其他规则时的默认规则，this指向**全局对象**。
  - **严格模式**下，this会绑定到`undefined`

* 2、隐式绑定

  * 当函数引用有**上下文对象**时，隐式绑定规则会把函数中的this绑定到这个上下文对象。对象属性引用链中只有上一层或者说最后一层在调用中起作用。
  * 被隐式绑定的函数特定情况下会丢失绑定对象，应用默认绑定，把this绑定到全局对象或者undefined上。

* 3、显示绑定

  * 通过`call(..)` 或者 `apply(..)`方法。显示绑定无法解决丢失绑定问题。
  * 硬绑定可以解决绑定丢失的问题。创建函数bar()，并在它的内部手动调用`foo.call(obj)`，强制把foo的this绑定到了obj。ES5内置了`Function.prototype.bind`，可以实现柯里化。

  ```js
  function foo() {
      console.log( this.a );
  }
  
  var obj = {
      a: 2
  };
  
  var bar = function() {
      foo.call( obj );
  };
  
  bar(); // 2
  setTimeout( bar, 100 ); // 2
  
  // 硬绑定的bar不可能再修改它的this
  bar.call( window ); // 2
  ```

* 4、new绑定

  ```js
  function create() {
      // 创建一个空的对象
      let obj = new Object()
      // 获得构造函数
      let Con = [].shift.call(arguments)
      // 链接到原型
      obj.__proto__ = Con.prototype
      // 绑定 this，执行构造函数
      let result = Con.apply(obj, arguments)
      // 确保 new 出来的是个对象
      return typeof result === 'object' ? result : obj
  }
  ```

* 5、箭头函数

  根据外层（函数或者全局）作用域（词法作用域）来决定this.**箭头函数的绑定无法被修改**(new也不行)。

  ```js
  function foo() {
      // 返回一个箭头函数
      return (a) => {
          // this继承自foo()
          console.log( this.a );
      };
  }
  
  var obj1 = {
      a: 2
  };
  
  var obj2 = {
      a: 3
  }
  
  var bar = foo.call( obj1 );
  bar.call( obj2 ); // 2，不是3！
  ```


优先级：new绑定 ----> 显示绑定 ----> 隐式绑定 ----> 默认绑定



#### Symbol的作用

表示独一无二的值。

Symbol 值通过`Symbol`函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

```js
let s = Symbol();

typeof s
// "symbol"
```

`Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。

`Symbol`函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。 

```js
// 没有参数的情况
let s1 = Symbol();
let s2 = Symbol();

s1 === s2 // false

// 有参数的情况
let s1 = Symbol('foo');
let s2 = Symbol('foo');

s1 === s2 // false
```



#### 实现一个promise.all，多个异步并发执行

```js
function PromiseAll(iterable) {
	var deferred = NewPromiseCapability(this);
	var resolutions = new InternalArray();
	function CreateResolveElementFunction(index, values, promiseCapability) {
    	var alreadyCalled = false;
    	return (x) => {
        	if (alreadyCalled === true) return;
        	alreadyCalled = true;
			//把这个promise实例的执行结果缓存到resolutions数组中
        	values[index] = x;
    	};
	}

	var i = 0;
	for (var value of iterable) {
		//把传入的参数用promise.resolve转化为Promise对象实例
    	var nextPromise = this.resolve(value);
		//转换完成后调用then方法执行
    	nextPromise.then(
        	CreateResolveElementFunction(i, resolutions, deferred),
        	deferred.reject
        );
    	++i;
	}
	return deferred.promise;
}
```

这里看起来就是把Promise.all传入的数组，用for of进行了遍历，然后对每一项先用promise.resolve(this.resolve)转化为Promise对象并且赋值给nextPromise，接着就调用nextPromise.then方法，把每一个promise对象的结果缓存到resolutions数组中：

```
values[index] = x
```

最后返回deferred.promise，当我们调用Promise.all([]).then的时候，得到的就是一个数组，里面的每一项元素对应传入的每一项Promise的处理结果。

至于提到的并行，应该说绝大部分时候我们指的是**异步操作**的并行 实际上来说:

```
Promise.all([异步操作1，异步操作2，异步操作3]).then
```

虽然依旧是循环先执行异步操作1.then，再执行异步操作2.then，但是这个for of循环**并不会等到异步操作1.then执行完成后再去执行异步操作2.then**，上面的代码很容易看到决定resolutions数组结果顺序的变量i是传入CreateResolveElementFunction函数的，因此这个for of循环体现出了这些异步操作的并行

> https://cnodejs.org/topic/587dbafac4f5cf76196714f1



#### 实现sum(2)(3)=>5

```js
var sum=function(i){ return function(j){return i+j;}}
undefined
sum(2)(3)
5
```



#### Promise实现的原理是什么，为什么能做到异步



> https://mengera88.github.io/2017/05/18/Promise%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90/

> https://chasecs.github.io/2017/10/07/simplified-chinese-translation-of-javascript-promises-in-wicked-detail.html



#### Async/Await怎么实现的异步转同步

> https://www.jianshu.com/p/c1b8b89c4905



#### class 限制



#### JS数组和对象的区别

这两种都是用来存储集合数据的，数组就好像继承了对象，它有对象的所有特点，并且自己还有扩展的属性和方法。还有几点区别如下： 1.数组是有序的数据集合，而对象是无序的。 2.数组的属性是0-n的整数，而对象的属性是任意的字符串(name)。

