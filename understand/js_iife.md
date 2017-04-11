## 理解即时函数IIFE

### 什么是即时函数IIFE
即时函数IIFE（Immediately-Invoked Function Expression）, 立即调用函数表达式，看看下面例子
```
(1+1)  // 输出 2，圆括号里作为表达式解析，如果换成函数呢？

function foo(){console.log(1)}
console.log(foo)                   // 输出函数 function foo(){console.log(1)}

(function foo2(){console.log(1)})  // 输出函数 function foo2(){console.log(1)}

foo()                                //调用函数foo
(function foo2(){console.log(1)})()  //同理，也可以这么调用函数foo2
```

上面的例子里都出现了圆括号，我们先来解释下圆括号的用途：

1. 当圆括号出现在函数的末尾，想要调用函数时，它会默认将函数当成是函数声明，不会执行函数。  
    ```
    function a(){console.log(1)}(1) //函数声明

    //等同于下面，没有执行函数a
    function a(){console.log(1)}
    (1)
    ```

2. 当圆括号包裹函数时，它会默认将函数作为表达式去解析，而不是函数声明。
    ```
    (function foo2(){console.log(1)})
    ```

通过上面例子，应该能理解什么是即时函数，简单的形式： `(fn)()` 这就是即时执行函数。


### 即时函数IIFE的应用

#### 创建闭包保存状态

我们来看看下面这个例子输出的是什么？

```
for(var i=0; i<5; i++){
    setTimeout(function(){
        console.log(i);
    },100);
}
```

上面将会输出5，5，5，5，5，不是预期那样输出0，1，2，3，4，那我们有没有办法做到呢？

```
for(var i=0; i<5; i++){
    setTimeout( (function(){
        console.log(i);
    })(i),100);
}
```

上面通过 即时函数IIFE `(fn)()` 对异步匿名函数包裹起来，这就创建了一个闭包，闭包里面保存了外部变量i的值，每次循环都创建一个闭包作用域，使得变量i不会被覆盖，关于闭包概念的理解，可以看看 [理解闭包](https://github.com/daviscai/get-node/blob/master/understand/js_closure.md)


#### 防止全局命名空间被污染

使用过Jquery的都知道这个用法
```
(function($) {
    //do something
})(Jquery);
```

可以把这种形式扩展下： `(function(p1,p2,...){})(p1,p2,...)`  把外部变量作为函数参数传入，变成局部变量，这样就可以避免全局变量冲突。


### 延伸阅读
https://segmentfault.com/a/1190000000523767
