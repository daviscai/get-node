## 理解设计模式 - 简单工厂模式

### 什么是简单工厂模式
又叫静态工厂方法，由一个工厂对象决定创建某一种产品对象类的实例。

简单工厂模式简单理解就是，你告诉工厂方法，你想要什么，工厂方法就会返回你想要的实例，你不需要关心具体实例是如何创建的细节，目的是为了隐藏实现，符合最少知识原则。

下面就是一个简单工厂模式的示例, LoginFactory就是工厂方法：
```
var LoginFactory = function(name){
  swith(name){
    case 'alert':
      return new LoginAlert();
      break;
    case 'confirm':
      return new LoginConfirm();
      break;
    case 'prompt':
      return new LoginPrompt();
      break;
  }
}
```


### 延伸阅读

简单工厂模式的缺点：  
1. 由于工厂类集中了所有产品创建逻辑，一旦不能正常工作，整个系统都要受到影响。  
2. 使用简单工厂模式将会增加系统中类的个数，在一定程序上增加了系统的复杂度和理解难度。  
3. 系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。  
4. 简单工厂模式由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。  


#### 工厂模式

为了解决多个类似对象声明的问题，我们可以使用一种叫做 工厂模式的方法，就是为了解决实例化对象产生大量重复代码的问题，增加新类，也无需修改工厂方法。

```
function Car(model,year,miles){//构造函数模式
    this.model = model;
    this.year = year;
    this.miles = miles;
    this.run = function () {
        return this.model + " has done " + this.miles + "miles";
    }
}
var Benz = new Car('Benz',2014,20000);
var BMW = new Car("BMW",2013,12000);
alert(Benz instanceof Car); //很清晰的识别他从属于 Car,true

console.log(Benz.run());
console.log(BMW.run());
```