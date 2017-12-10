## 理解设计模式 - 访问者模式

### 什么是访问者模式
GOF官方定义比较难懂，通俗点讲，访问者模式就是：
先把一些可复用的行为抽象到一个函数(对象)里，这个函数我们就称为访问者（Visitor）。如果另外一些对象要调用这个函数，只需要把那些对象当作参数传给这个函数，在js里我们经常通过call或者apply的方式传递this对象给一个Visitor函数.

访问者模式也被称为GOF总结的23种设计模式中最难理解的一种。在强类型语言中需要通过多次重载来实现访问者的接口匹配。

而在js这种基于鸭子类型的语言中，访问者模式几乎是原生的实现, 所以我们可以利用apply和call毫不费力的使用访问者模式。

访问者模式符合单一职责原则，`把可复用的行为抽象到一起`。

### Javascript实现

利用访问者模式，给一个object对象增加push方法：
```
var Visitor = {} 
Visitor.push  =  function(){ 
    return Array.prototype.push.apply( this, arguments ); 
} 
var obj = {}; 
obj.push = Visitor.push; 
obj.push( "first" ); 
alert ( obj[0] )  //"first" 
alert ( obj.length );  //1 
```
