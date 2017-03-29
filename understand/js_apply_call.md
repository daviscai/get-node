### 理解 apply 和 call

可实现对某个方法的功能扩展，fn.call 和 fn.apply 调用时候会立即执行，且不会返回一个新函数，这点跟 fn.bind 不一样，bind方法不会马上执行函数，而是返回一个新函数。

下面我们来实现一个需求，对一个已知的对象方法进行扩展，在执行该方法前输出 do brefor, 执行该方法后输出 do after，这种场景在一些MVC框架中非常常见。

```
var m = {
  do:function(a){
    console.log('do something: '+a);
  }
}

function abc(){
    var fn = m['do']

    m['do'] = function(){  //此处不能用箭头函数，因为箭头函数没有arguments参数
		console.log('do brefor');

        fn.apply(this, arguments);             // JS的任何一个方法都会有一个隐形参数 arguments，类似数组
        //fn.call(this, [...arguments]);       // call接收的是参数列表形式，需要用扩展运算符转换成参数列表
        //let f = fn.bind(this, ...arguments); // bind不会马上执行函数，而是返回一个新函数
        //f();

		console.log('do after');
    }
}
abc();
m.do('kkkk');
```

输出：  
```
do brefor
do something: kkkk
do after
```

其实，上面的例子去掉abc()方法的封装，也是可行的，比如：

```
var m = {
  do:function(a){
    console.log('do something: '+a);
  }
}

//function abc(){
    var fn = m['do']

    m['do'] = function(){  //此处不能用箭头函数，因为箭头函数没有arguments参数
		console.log('do brefor');

        fn.apply(this, arguments);             // JS的任何一个方法都会有一个隐形参数 arguments，类似数组
        //fn.call(this, [...arguments]);       // call接收的是参数列表形式，需要用扩展运算符转换成参数列表
        //let f = fn.bind(this, ...arguments); // bind不会马上执行函数，而是返回一个新函数
        //f();

		console.log('do after');
    }
//}
//abc();
m.do('kkkk');
//window.m.do('kkkk'); 结果一样
```


通过上面例子可以直观的感受下apply 和call 的作用，下面我们来分析下如何做到的？

原理比较简单，因为我们对m.do()方法重写了，具体步骤如下：
1. 重写之前把对象方法先赋值给一个变量 `var fn = m['do']`
2. 重写的函数也叫 m.do 或 m['do']，在新函数里通过apply或者call，把执行上下文this绑定到新函数，fn.apply(this, arguments) 里的this是指向abc()函数或者window全局对象的，这样带来的改变是，我们运行 `m.do()` 时，其实是运行`window.m.do()`，这个就是我们重写的新函数，自然就会先执行 `console.log('do brefor');`，接着执行存储在临时变量的fn函数，最后执行 `console.log('do after');` ，从而实现了对已有函数的扩展。


该场景是在实际工作中遇到的问题，抽象出来的模型，可以加深对apply 和call 应用场景的理解。
