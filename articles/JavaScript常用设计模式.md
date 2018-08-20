### JavaScript常用设计模式



#### 单例模式

单例就是保证一个类只有一个实例，实现的方法一般是先判断实例存在与否，如果存在直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象。

在JavaScript里，单例作为一个命名空间提供者，从全局命名空间里提供一个唯一的访问点来访问该对象。

单例一般用在系统间各种模式的通信协调上。

```js
var SingletonTester = (function () {

    // 参数：传递给单例的一个参数集合
    function Singleton(args) {

        // 设置args变量为接收的参数或者为空（如果没有提供的话）
        // 只有在使用的时候才初始化
        var args = args || {};
        // 设置name参数
        this.name = 'SingletonTester';
        // 设置pointX的值
        this.pointX = args.pointX || 6; // 从接收的参数里获取，或者设置为默认值
        // 设置pointY的值
        this.pointY = args.pointY || 10;

    }

    // 实例容器
    var instance;

    var _static = {
        name: 'SingletonTester',

        // 获取实例的方法
        // 返回Singleton的实例
        getInstance: function (args) {
            if (instance === undefined) {
                instance = new Singleton(args);
            }
            return instance;
        }
    };
    return _static;
})();

var singletonTest = SingletonTester.getInstance({ pointX: 5 });
console.log(singletonTest.pointX); // 输出 5 
```





#### 工厂模式

工厂模式创建对象（视为工厂里的产品）时无需指定创建对象的具体类。

工厂模式定义一个用于创建对象的接口，这个接口由子类决定实例化哪一个类。该模式使一个类的实例化延迟到了子类。而子类可以重写接口方法以便创建的时候指定自己的对象类型。

这个模式十分有用，尤其是创建对象的流程赋值的时候，比如依赖于很多设置文件等。并且，你会经常在程序里看到工厂方法，用于让子类类定义需要创建的对象类型。

以下几种情景下工厂模式特别有用：

* 对象的构建十分复杂
* 需要依赖具体环境创建不同实例
* 处理大量具有相同属性的小对象

```js
var productManager = {};

productManager.createProductA = function () {
    console.log('ProductA');
}

productManager.createProductB = function () {
    console.log('ProductB');
}
        
productManager.factory = function (typeType) {
    return new productManager[typeType];
}

productManager.factory("createProductA");
```





#### 建造者模式

建造者模式可以将一个复杂对象的构建和表示分离，使得同样的构建过程可以创建不同的表示。也就是说如果我们用了建造者模式，那么用户只需要指定建造的类型就可以得到它们，而具体建造的过程和细节就不需要知道了。

网络请求框架Fetch中有很多回调函数（比如success, error回调等），这些回调就采用了建造者模式，主要目的就是**职责分离**。

```js
function getBeerById(id, callback) {
    // 使用ID来请求数据，然后返回数据.
    asyncRequest('GET', 'beer.uri?id=' + id, function (resp) {
        // callback调用 response
        callback(resp.responseText);
    });
}

var el = document.querySelector('#test');
el.addEventListener('click', getBeerByIdBridge, false);

function getBeerByIdBridge(e) {
    getBeerById(this.id, function (beer) {
        console.log('Requested Beer: ' + beer);
    });
}
```

根据建造者的定义，表相即是回调，也就是说获取数据以后如何显示和处理取决于回调函数，相应地回调函数在处理数据的时候不需要关注是如何获取数据的。

下面的jQuery例子中，只需要传入要生成的HTML字符，而不需要关系具体的HTML对象是如何生产的。

```js
$('<div class= "foo"> bar </div>');
```





#### 观察者模式

观察者模式又叫发布-订阅模式（Publish/Subscribe），它定义了一种一对多的关系，让多个观察者对象同时监听某一个主题对象，这个主题对象的状态发生变化时就会通知所有的观察者对象，使得它们能够自动更新自己。

使用观察者模式的好处：

1. 支持简单的广播通信，自动通知所有已经订阅过的对象。
2. 页面载入后目标对象很容易与观察者存在一种动态关联，增加了灵活性。
3. 目标对象与观察者之间的抽象耦合关系能够单独扩展以及重用。

```js
let yourMsg = {};
yourMsg.peopleList ={};
yourMsg.listen = function (key,fn) {
    if (!this.peopleList[key]) { //如果没有订阅过此类消息，创建一个缓存列表
        this.peopleList[key] = [];
    }
    this.peopleList[key].push(fn);
}
yourMsg.triger = function () {
    let key = Array.prototype.shift.call(arguments);
    let fns = this.peopleList[key];
    if (!fns || fns.length == 0) {//没有订阅 则返回
        return false;
    }
    for(var i=0,fn;fn=fns[i++];){
        fn.apply(this,arguments);
    }
}

yourMsg.listen('marrgie',function (name) {
    console.log(`${name}想知道你结婚`);
})
yourMsg.listen('unemployment',function (name) {
    console.log(`${name}想知道你失业`);
})

yourMsg.triger('marrgie','张三');
yourMsg.triger('unemployment','李四');
```





#### 原型模式

原型模式（prototype）是指用原型实例指向创建对象的种类，并且通过拷贝这些原型创建新的对象。

对于原型模式，我们可以利用JavaScript特有的原型继承特性去创建对象的方式，也就是创建的一个对象作为另外一个对象的prototype属性值。原型对象本身就是有效地利用了每个构造器创建的对象。

```js
var vehicle = {
    getModel: function () {
        console.log('车辆的模具是：' + this.model);
    }
};

var car = Object.create(vehicle, {
    'id': {
        value: MY_GLOBAL.nextId(),
        enumerable: true // 默认writable:false, configurable:false
 },
    'model': {
        value: '福特',
        enumerable: true
    }
});
```



