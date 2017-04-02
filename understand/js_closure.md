## 理解闭包

提到闭包，很多人第一反应就是什么鬼？ 长这么大第一次听这个词啊。。。

我们来看看维基百科的介绍：
>闭包（英语：Closure），又称词法闭包（Lexical Closure）或函数闭包（function closures），是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。在函数中可以（嵌套）定义另一个函数时，如果内部的函数引用了外部的函数的变量，则可能产生闭包。
-- 维基百科


我们可以这么理解闭包的概念：
1. 闭包是一个作用域；
2. 闭包是包含了创建时的变量和函数的作用域，而且这个作用域是独立的，就算创建时的作用域消失，创建后的作用域依然存在，直到闭包里的变量和函数没有被任何地方引用；
3. 在函数中（嵌套）定义另一个函数时，如果内部的函数引用了外部的函数的变量，则可能产生闭包。

先不管这个概念，先看看例子，看完后再回来体会下，理解会更深刻。

注：为了更好理解闭包，下面例子部分来自《JavaScript忍者秘籍》一书。

```
var outerValue = 'ninja';
function outerFunction(){
    assert(outerValue == 'ninja', "I can see the ninja")
}
outerFunction();
```
这就是闭包！！！， 什么？我们每天都在写闭包？，别忽悠我读书少！

这的确是闭包，我们来看看，其实闭包很简单，对吧？！

因为 outValue 和 outerFunction 的作用域是全局域(global scope)，而全局域是永远不会消失的(自从页面被加载就存在了)

相当于在全局作用域下创建了一个函数(函数作用域)，在函数里面调用了外部作用域(此处是全局作用域)的变量outerValue，这就产生了闭包！

所以，闭包是一个包含了创建时的变量和函数的作用域，但这样的闭包没有起到什么作用，所以我们感觉不到。

我们把上面的极简代码加点料：

```
var outerValue = 'ninja';   

function outerFunction(){       
    var innerValue = 'samurai';   

    function innerFunction(){       
        console.log(outerValue);   
        console.log(innerValue);      
    }   
	innerFunction();
}   

outerFunction();  
```

输出：
```
ninja
samurai
```

innerFunction函数创建后，在调用外部变量时，就产生了闭包(想象成产生了一个气泡，气泡里包含了变量和函数的引用，与其他变量和函数隔离)，在闭包内可以访问外部变量和函数。

但我们无法在全局作用域下访问innerValue变量，因为innerValue变量的作用域在outerFunction函数里，我们可以这样做

```
var outerValue = 'ninja';   
var later = ''; //添加临时变量
function outerFunction(){       
    var innerValue = 'samurai';   

    function innerFunction(){       
        console.log(outerValue);   
        console.log(innerValue);      
    }   
	later = innerFunction; //把内部函数赋值给变量
}   

outerFunction();  
later();
```
输出结果同上，但执行过程有点不一样，现在是在全局作用域调用了later(), 等同于调用了innerFunction(), 那为什么可以在全局作用域下访问到innerValue变量呢？ innerValue变量的作用域不在全局，应该是不可见。

这就是闭包产生了作用，当我们创建 innerFunction 的时候，不仅仅是定义了一个 function，我们还创建了一个域。 在外部掉用 innerFUnction 的时候，虽然调用发生在全局域，但是innerFunction 还是会访问当初被定义时的那个原始域，这个域就是它的闭包（closure）。


其实，上面的代码我们经常看见，甚至曾经写过，一切都感觉很自然，所以不会太在意为什么能这样，现在应该有点明白了吧？ 是因为创建了闭包！ 上面例子其实创建了2个闭包。

我们再来看看下面例子，闭包的威力可以更强
```
var outerValue = 'ninja';   
var later = ''; //添加临时变量
function outerFunction(){       
    var innerValue = 'samurai';   

    function innerFunction(paramValue){       
        console.log(outerValue);   
        console.log(innerValue);
        console.log(paramValue);
        console.log(tooLate);

    }   
	later = innerFunction; //把内部函数赋值给变量
}   

var tooLate = 'ronin';

outerFunction();  
later('wakizashi');  // 有参数
```

输出：
```
ninja
samurai
wakizashi
ronin
```

通过输出结果，我们知道2个特性：
1. 参数也在闭包内，innerFunction的参数一样可以被访问到；
2. 即使在闭包创建后定义的变量，闭包内也能访问到，可以访问到tooLate变量。


闭包很难引起注意，也没有办法检测到闭包这个东西，但闭包是会占用内存的，下面2种方式可以释放内存：
1. GC垃圾回收机制
2. 闭包作用域里的所有引用都消失了


### 闭包的应用场景


#### 创建私有变量

在面向对象语言创建私有变量非常简单，只需要声明为private就可以了，但是JavaScript里行不通，我们可以利用闭包的特性来做到，看代码

```
function Ninja(){       
    var slices = 0;   
    this.getSlices = function(){
        return slices;
    }

    this.slice = function(){
        slices++;       
    }
}

var ninja = new Ninja();    
ninja.slice();
```

函数内部的变量，只允许 Ninja 的内部方法来访问，这种方式我们还是感觉不到闭包的作用，因为面向对象的编程经验告诉我们这样很正常，但对于Javascript来讲，确实是闭包的作用。

#### Callback回调

没错，回调也是闭包的应用场景，比如：

```

jQuery('#testButton').click(function(){
    var $elem = jQuery("div");       
    $elem.heml("Loading...");   
    jQuery.ajax({
        url: test.html,           
        success: function(){
            console.log($elem)  // 可以访问到外部变量$elem
            // do something
        }
    })
}
```

#### 定时器 setTimeout, setInterval


#### 函数重载


#### 即使函数


#### 类库封装


### 总结

闭包在Javascript编程里无处不在，也让JS语言具有更多特性，更重要的是，我们都感觉不到它的存在，这种感觉很奇妙。
