## 理解设计模式 - 单例模式

### 什么是单例模式
`单例模式的定义是产生一个类的唯一实例`，但对于Javascript非面向对象语言来说，更重要的是借鉴解决问题的思想，单例模式在任何情况下都只有一次创建行为，无论是类实例化还是创建数据库链接，还是创建一个div弹窗，可以确保只有一个对象，不会重复创建。


### Javascript实现

下面是比较常用方式实现单例模式
```
var getSingle = function(fn){
    var result;
    return function (){  // 惰性函数 ，调用 getSingle()方法时不执行
		console.log('result:'+result)
		if(!result){
			result = fn.apply(this,arguments);
		}
		return result;
    };
};

//创建div登录框
var createLoginLayer=function (){
    var div= document.createElement('div');
    document.body.appendChild(div);
    return div;
};

var create = getSingle(createLoginLayer);

var a = create();
var b = create();

```

输出：
```
result:undefined
result:[object HTMLDivElement]
```

结果跟我们预期的一样，第二次调用create()后，已经获取到了上一次创建的对象，不会再创建一次了，无论调用多少次，总是返回第一次创建的对象。


下面看看比较接近实际项目的单例应用

```
var single = (function() {
    var _u;//私有成员，
    var newModel = function() {//需要创建的类
        this.a = 'i am a';
        this.b = 'i am b';
        this.hello = 'i am a single Model';
    }
    newModel.prototype = {//原型扩展
        constructor : newModel,
        fa : function() {
            console.log(this.a);
        },
        fb : function() {
            console.log(this.b);
        },
        fhello : function() {
            console.log(this.hello);
        }
    }
    return {
        createSingleModule : function() {//暴露共有方法
            return (_u || (_u = new newModel()));//如果该类实例过了，就返回。没有则重新实例化。
        }
    }
})();

//创建一个单例的实例化对象，不管此函数执行多少次，返回的对象总是同一个。newModel只会被实例化一次。
single.createSingleModule();

```

### 延伸阅读
http://www.alloyteam.com/author/tat-svenzeng/  
http://www.cnblogs.com/TomXu/archive/2012/02/20/2352817.html  
http://frontenddev.org/link/javascript-design-mode-7-appearance.html  
