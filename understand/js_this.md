## 理解this作用域

首先来看看this是什么？ this是函数执行上下文对象的一个属性，存在于运行时，而不是定义时，也就是说`调用时使用了谁的上下文，this就指向谁`，理解这点非常重要，下面通过实际例子来理解这句话，其实非常简单。
```
class A{
	constructor() {
      console.log(this);

  	}
	getid(){
		console.log(this);
		console.log('============212222');
		this.getaa();
	}

	getaa(){
		console.log('====');
	}
}

let a = new A()
a.gitid();
```

正常如期輸出:
```
A {}
A {}
============212222
====
```
因为调用this时候，上下文就是class A的实例，所以指向了class A，这么使用完全没有问题。

现在改变下调用方式，有一个函数要调用 class A实例的方法
```
class A{
	constructor() {
      console.log(this);

  	}
	getid(){
		console.log(this);
		console.log('============212222');
		this.getaa();
	}

	getaa(){
		console.log('====');
	}
}

function B(fn){
	fn();
}
let a = new A();
B(a.getid);
```

报错:
```
A {}
undefined
============212222
Uncaught TypeError: Cannot read property 'getaa' of undefined
```

class A构造函数里的this是正确的，指向了A实例，报错是因为`运行时的上下文是B函数的上下文`，而不是class A实例，this变成了undefined，是因为B函数的上下文在调用过程丢失了，这样说明：`调用时使用了谁的上下文，this就指向谁`， 此时，是不是明白了点什么？ 继续往下看。


那我们怎么让上下文重新绑定到class A实例上呢？ 其实JS提供了3种方法apply，call和 bind，如果对它们不了解的，可以先看看 [理解apply和call](https://github.com/daviscai/get-node/blob/master/understand/js_apply_call.md) ， 通过这个例子也可以加深对它们使用场景的理解

```
class A{
	constructor() {
      console.log(this);

  	}
	getid(){
		console.log(this);
		console.log('============212222');
		this.getaa();
	}

	getaa(){
		console.log('====');
	}
}

function B(a, fn){
	fn.apply(a, arguments);      		// JS的任何一个方法都会有一个隐形参数 arguments，类似数组
	//fn.call(a, ...arguments);  		// call接收的是参数列表形式，需要用扩展运算符转换成参数列表
	//let f = fn.bind(a, ...arguments);  // bind不会马上执行函数，而是返回一个新函数
    //f();
}
let a = new A();
B(a, a.getid);
```

通过 apply 或者 call 把函数执行上下文改为class A实例，关于this作用域非常容易被晦涩难懂的概念搞蒙圈，以后用这个简单的例子来理解就明白了

记住2点：  
1. 调用时使用了谁的上下文，this就指向谁   
2. 可以用 apply, call, bind 改变上下文


### ES6 新特性：箭头函数里的This

如果对箭头函数不了解的，可以先看看阮一峰老师的[ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/function#箭头函数)

箭头函数有几个使用注意点。

（1）函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。  
（2）不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。    
（3）不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。   
（4）不可以使用yield命令，因此箭头函数不能用作Generator函数。  

上面四点中，第一点尤其值得注意。在普通函数中，this对象的指向是可变的，但是在箭头函数中，它是固定的。

引用阮老师的例子：
```
请问下面的代码之中有几个this？

function foo() {
  return () => {
    return () => {
      return () => {
        console.log('id:', this.id);
      };
    };
  };
}

var f = foo.call({id: 1});

var t1 = f.call({id: 2})()(); // id: 1
var t2 = f().call({id: 3})(); // id: 1
var t3 = f()().call({id: 4}); // id: 1
```
上面代码之中，只有一个this，就是函数foo的this，用call改变上下文也没用，箭头函数里的this是定义时就固定了。


### 扩展阅读：  
https://zhuanlan.zhihu.com/p/25294187  
http://www.jianshu.com/p/881a1ef2e049
