## 理解设计模式 - 观察者模式

### 什么是观察者模式
观察者模式又叫发布订阅模式（Publish/Subscribe），它定义了一种一对多的关系，让多个观察者对象同时监听某一个主题对象，这个主题对象的状态发生变化时就会通知所有的观察者对象，使得它们能够自动更新自己。

观察者模式可以很好的实现2个或多个模块之间的解耦。 

观察者模式的应用场景非常多，比如JS的事件监听就是观察者模式，还有比如一个页面里有多个模块，需要在用户登录后（异步无刷新登录）更新数据，这个时候就可以通过订阅（监听）用户登录事件来做到。

### Javascript实现


nodejs核心模块Events提供EventEmitter对象，也实现了分布式事件。如下代码：

```
var Emitter = require('events').EventEmitter;

var emitter = new Emitter();

emitter.on('someEvent',function(stream){

console.log(stream + 'from eventHandler1');

});

emitter.on('someEvent',function(stream){

console.log(stream + 'from eventHandler2');

});

emitter.emit('someEvent','I am a stream!');
```
emitter对象中的 emitter.on是指发布事件”someEvent”，而emitter.emit是指触发事件,事件名称为”someEvent”.从而执行回调函数.


参考EventEmitter对象的思路，我们来看看怎么写订阅/发布

```
function PubSub() {
    this.handlers = {};
}
PubSub.prototype = {
    // 订阅事件
    on: function(eventType,handler){
        var self = this;
        if(!(eventType in self.handlers)) {
             self.handlers[eventType] = [];
        }
        self.handlers[eventType].push(handler);
        return this;
       },
       // 触发事件(发布事件)
       emit: function(eventType){
           var self = this;
           var handlerArgs = Array.prototype.slice.call(arguments,1);
           for(var i = 0; i < self.handlers[eventType].length; i++) {
                 self.handlers[eventType][i].apply(self,handlerArgs);
           }
           return self;
       }
};// 调用方式如下：

var pubsub = new PubSub();

pubsub.on('A',function(data){

console.log(1 + data);  // 执行第一个回调业务函数

});

pubsub.on('A',function(data){

console.log(2 + data); // 执行第二个业务回调函数

});

// 触发事件A

pubsub.emit('A',"我是参数");
```


### 延伸阅读

#### MVVM双向绑定原理

MVVM双向绑定原理就应用了观察者模式，订阅发布路径是这样的：

1. Data to View(UpdateView订阅了Model的change事件)
    a. 首先设置一个UpdateView函数，传入Data就更新View
    b. Object.defineProperty拦截对象的get和set参数，当发生属性变化时候额外触发UpdateView更新View

2. View to Data(UpdateModel订阅了元素的press、change之类事件)
    a. 绑定事件 触发时候修改Model,设这个动作叫做UpdateModel
    b. 触发后进行数据更改

换个通俗的说法就是：Data在改变后触发回调更新View，而View改动后触发回调更新Data。

这里使用观察者模式有两个目的:  
    1. 解耦:目标对象与观察者之间的抽象耦合关系能够单独扩展以及重用
    2. 1对多:一个数据可能在一个View里面多处使用。