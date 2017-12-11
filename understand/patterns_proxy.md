## 理解设计模式 - 代理模式

### 什么是代理模式

代理，顾名思义就是帮助别人做事，GoF对代理模式的定义如下：
> 代理模式（Proxy），为某个对象提供一种代理以控制对这个对象的访问。

代理模式非常具有辨识度的特性就是，通过实现和对象相同接口的手段来代替这个对象，也就是说代理者和本体（被代理者）具有相同的接口，他们长得完全一样。

问题来了，他们长得一样，我们为什么要使用一个代理去代替原始对象呢？

我们先来看看代理模式的伪代码：
```
/*  为了清楚，我们不展示具体的实现代码 */
var CarList = function() {
    //创建
};

CarList.prototype = {
    getCar: function(...) {
        // 用传进来的参数创建车
    },

    search: function(...) {
        // 用传进来的条件查询车
    },

    addCar: function(...) {
        // 往数据库里添加一辆车
    },
    .
    .
    .
};

var CarListProxy = function() {
    this.carList = new CarList();
};

CarListProxy.prototype = {
    getCar: function(...) {
        return this.carList.getCar(...);
    },

    search: function(...) {
        return this.carList.search(...);
    },

    addCar: function(...) {
        return this.carList.addCar(...);
    },
    .
    .
    .
};
```
CarListProxy代理了CarList，并且拥有跟CarList一样的接口，这样做的好处就是： `延迟一个大对象的实例化，访问控制`。

假如CarList具有非常多的方法，那么它的实例就是一个非常大的对象，如果我们只用到了其中几个方法，没有必要浪费内存存储其他没有用到的方法，这个时候就可以通过代理来做到，只代理部分接口即可。


代理模式一般适用于如下场合，或者说按下面的场合来划分代理：

1. 远程代理：
远程代理一种常用的代理模式，它使得客户端程序可以访问在远程主机上的对象，通常是指网线的另一端，而我们访问服务器端的JavaScript的可能性并不大。

2. 虚拟代理
虚拟代理(Virtual Proxy)也是一种常用的代理模式，对于一些占用系统资源较多或者加载时间较长的对象，可以给这些对象提供一个虚拟代理。`在真实对象创建成功之前虚拟代理扮演真实对象的替身`，而当真实对象创建之后，虚拟代理将用户的请求转发给真实对象。

我们把上面的代码稍微改下就是虚拟代理了

```
var CarListProxy = function() {
    // 先不初始化CarList.
    this.carList = null;
};
CarListProxy.prototype = {
    // 其他方法在任何时候都可以调用这个方法，
    // 为的是在必要的时候初始化CarList
    _init: function() {
        if (!this.carList) {
            this.carList = new CarList();
        }
    },

    getCar: function(...) {
        // 每个方法都会调用 _init()
        this._init();
        return this.carList.getCar(...);
    },

    search: function(...) {
        this._init();
        return this.carList.search(...);
    },

    addCar: function(...) {
        this._init();
        return this.carList.addCar(...);
    },
    .
    .
    .
}
```
CarListProxy代理初始化时并没有实例化CarList，但CarListProxy已经具有CarList一样的接口，扮演真实对象的替身，执行 this._init()方法，真实对象创建后，才会转发请求真实对象。


3. 缓存代理
缓存代理(Cache Proxy)也是一种较为常用的代理模式，它为某一个操作的结果提供临时的缓存存储空间，以便在后续使用中能够共享这些结果，从而可以避免某些方法的重复执行，优化系统性能。

```
//向后台发请求，获取当前页面的数据
// http.getPage(page);
var pageProxy = (function(){
    var cache = {};
    return function(fn){   //fn作为处理页码数据的函数
        var pageData = cache[page]; // 缓存数据
        if(pageData){
            return fn(pageData);  //返回制定页码的数据
        }
        http.getPage(page)   //获取制定页码的数据
        .then((data)=>{
            cache[page] = data;  //存放数据
            fn(data);
        })
    }
})();
```

4. 安全代理（保护代理）
保护代理就是起到保护作用，用来过滤掉一下不必要的请求，将真正需要的递给本体。比如可以通过代理先进行安全检查，验证后再请求真实对象。

看下伪代码：
```
//譬如，验证用户名是否唯一
//这里我们应用，保护代理的思想，如果用户名是不合法的，则不会将该请求给本体执行
var checkUser = function(name){
    // ajax 异步请求验证用户名是否存在
}
var proxy = (function(){
    var user = document.querySelector("#username");
    return function(){
        var userName = user.value;
        if(!userName){ // 这里只是判空，也可以做其他的安全检查
            return;
        }
        // 检查通过后再请求
        checkUser(userName);
    }
})();
```