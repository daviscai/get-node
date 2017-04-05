## 理解原型和原型链

Javascript里的任何一个函数，都有一个隐性属性 prototype，这个隐性属性的方法就是这个函数的原型方法，我们可以自定义原型方法，比如对数组自定义一个原型方法：

```
Array.prototype.say = function(){
    console.log('hi');
}
var a = new Array()  // 或者 var a = []
a.say()
```
输出：
```
hi
```

这就是原型的作用，通过原型可以非常方便的扩展一个已有函数的功能。

### 对象实例化

原型存在于函数，是函数的一个属性，但只有通过构造器 new 关键字实例化之后，原型方法才能被调用，比如下面代码是会报错的
```
function abc(){

}
abc.prototype.say = function(){
    console.log('hi');
}
abc.say()
```

输出：
```
Uncaught TypeError: abc.say is not a function
```

上面报错是因为没有进行实例化而直接调用了say()方法，say()方法是原型方法，必须通过实例化后，才能调用，如下：
```
function abc(){

}
abc.prototype.say = function(){
    console.log('hi');
}
var a = new abc() // 如果是 var a = abc(), a等于什么？ undefined
a.say()
```
输出：
```
hi
```

### 实例方法和原型方法的优先级

假如实例方法已经存在，我们又添加了同名的原型方法，这个时候哪个优先级更高？ 我们还是以上面代码为例：

```
function abc(){
  this.say = function(){
	console.log('from instance');
  }
}
abc.prototype.say = function(){
    console.log('from prototype');
}
var a = new abc()
a.say()
```
输出：
```
from instance
```
例子说明，实例方法的优先级更高，这是因为通过构造器实例化后，this上下文指向的是实例对象本身，会优先调用实例对象的方法，如果没有才会找原型方法。


### 对象的constructor属性

每个对象都有一个名为constructor的属性，该属性引用的是创建该对象的构造器，比如上面的例子：

```
function abc(){
  this.say = function(){
	console.log('from instance');
  }
}
abc.prototype.say = function(){
    console.log('from prototype');
}
var a = new abc()
console.log(new a.constructor);
```

输出：
```
abc {}
   say: ()
   __proto__: Object
```
简单地讲就是，new abc() 创建实例对象a， a.constructor就指向创建对象a的构造器abc，所以，可以用 new a.constructor来实例化新对象，等同于 new abc()。


那么，这个有什么用呢？ 假设我们只知道对象实例，但不知道这个对象是什么，而且我们又需要创建一个新的实例，这个时候就可以用 new a.constructor 类似方式来做到，另外还有一个比较常见的应用场景： 判断对象类型

通常有3种判断对象类型的方法，如下：

```
function abc(){}

var a = new abc();

console.log( typeof a === "object")  //粗粒度，仅仅判断为对象类型
console.log( a  instanceof abc )     //细粒度
console.log( a.constructor == abc )  //细粒度
```

输出：
```
true
true
true
```


### 继承和原型链

Javascript在ES6标准之前没有面向对象的类继承机制，在ES6后新增了class和extends才有了类继承机制。

首先，我们来看看ES5如何实现继承，
```
function Super() {}

function Sub() {}
Sub.prototype = new Super();  // 父类的实例赋值给子类的原型，是实现继承的关键

var sub = new Sub();

console.log(Sub.prototype.constructor);
console.log(sub.constructor);
console.log(sub.__proto__ === Sub.prototype);
console.log(Sub.prototype.__proto__ == Super.prototype);
```

输出：
```
function Super() {}
function Super() {}
true
true
```

`父类的实例赋值给子类的原型，是实现继承的关键`，这种方式也被称为`原型链`。我们再来看看ES6对继承的支持：

```
class Super {}

class Sub extends Super {}

var sub = new Sub();

console.log(Sub.prototype.constructor);
console.log(sub.constructor);
console.log(sub.__proto__ === Sub.prototype);
console.log(Sub.__proto__ === Super);
console.log(Sub.prototype.__proto__ === Super.prototype);
```

输出：

```
class Sub extends Super {}
class Sub extends Super {}
true
true
true
```

可以看到ES6已支持类继承，与ES5通过原型链实现继承的结果是一样的。

ES6的class和extends语法里同样支持原型方法，如下：

```
class Super {}

class Sub extends Super {}

Sub.prototype.say = function(){   // 原型方法
	console.log('Hi');
}

var sub = new Sub();
sub.say();  // 输出 Hi
```
