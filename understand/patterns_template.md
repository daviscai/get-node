## 理解设计模式 - 模式方法模式

### 什么是模式方法模式
定义一个操作中算法的框架（骨架），而算法的具体实现由具体的子类实现，使得子类可以不改变算法的结构即可重定义该算法中的某些特定步骤。

先看看伪代码会比较清楚：

假如写一个汽车检测方法，完成测试的常规检测，抽象方法，定义算法的框架
```
var CarCheck = {
    check : {
        this.startup();
        this.speeup();
        this.checkspeed();
        this.brake();
        this.stop();
    }    
}
```

不同的汽车有相同的检测步骤（环节）,但检测的标准和指标，不同汽车不一样，这样就要在各个汽车类实现。

```
function Porsche911() {
  this.__proto__ = new CarCheck();
  this.startup = function () {
    console.log("检测911启动");
  };
  this.speeup = function () {
    console.log("检测911加速");
  };
  this.brake = function () {
    console.log("检测911刹车");
    //检测方法标准
    console.log("911刹车合格");
  };
  this.stop = function () {
    console.log("检测911熄火");
  }
}

function Porsche781() {
  this.__proto__ = new CarCheck();
  this.startup = function () {
    console.log("检测781启动");
  };
  this.speeup = function () {
    console.log("检测781加速");
  };
  this.brake = function () {
    console.log("检测781刹车");
    //检测方法标准
    console.log("781刹车合格");
  };
  this.stop = function () {
    console.log("检测781熄火");
  };
  this.checkspeed = function () {
    console.log("检测781速度");
  };
}


var porsche911 = new Porsche911();
var porsche781 = new Porsche781();

porsche911.check();
porsche781.check();
```

模板方法模式优点:  
1. 容易扩展。一般来说，抽象类中的模版方法是不易反生改变的部分，而抽象方法是容易反生变化的部分，因此通过增加实现类一般可以很容易实现功能的扩展，符合开闭原则。

2. 便于维护。对于模版方法模式来说，正是由于他们的主要逻辑相同，才使用了模版方法。