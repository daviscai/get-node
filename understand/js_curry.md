## 理解Curring柯里化

>Curring的概念很简单：只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。

Curring柯里化，俗称“部分求值”。一个柯里化函数首先是会接受一些参数，但是接受这些参数之后，该函数并不会立即求值，而是继续返回另一个函数，刚才传入的参数在函数形成的闭包中被保存起来。等到函数被真正需要求值的时候，之前传入的所有参数会被一次性用于求值。

那为什么叫 Curring（柯里化）呢？
>The name “currying”, coined by Christopher Strachey in 1967, is a reference to logician Haskell Curry[引自维基百科， currying， naming]
柯里化命名来源于Haskell Curry，Moses Schönfinkel提出这个概念后，Haskell Curry通过计算机实现了它。后来于1967年由Christopher Strachey命名为curying。

我们来看看代码

```
var add = function(x) {
  console.log('x='+x)
  return function(y) {
    console.log('y='+y);
    return x + y;
  };
};

var increment = add(1);
console.log('-------');
increment(2);
```

输出：
```
x=1
-------
y=2
3
```

这里我们定义了一个 add 函数，它接受一个参数并返回一个新的函数。调用 add 之后，返回的函数就通过闭包的方式记住了 add 的第一个参数，此时，返回的函数依然可以接收参数（处理其他参数）,这就是Curring柯里化。

其实，上面的例子是Curring柯里化的定义模型，告诉我们函数的调用可以这么做，相当于只是一个概念，实际上，我们的函数是这样的

```
function add(x,y){
  return x+y;
}
```

而我们希望通过一个转换函数，把add(x,y)转换后符合柯里化定义，这个转换函数就是Curring（柯里化）函数，比如 lodash库已实现了这个函数：

```
var curry = require('lodash').curry;

var match = curry(function(what, str) {
  return str.match(what);
});

match(/\s+/g)("hello world");  // [ ' ' ]
```

lodash.curry 代码：

```
import createWrap from './.internal/createWrap.js'
/**
 * @since 2.0.0
 * @category Function
 * @param {Function} func The function to curry.
 * @param {number} [arity=func.length] The arity of `func`.
 * @returns {Function} Returns the new curried function.
 * @example
 *
 * const abc = function(a, b, c) {
 *   return [a, b, c]
 * }
 *
 * const curried = curry(abc)
 *
 * curried(1)(2)(3)
 * // => [1, 2, 3]
 *
 * curried(1, 2)(3)
 * // => [1, 2, 3]
 *
 * curried(1, 2, 3)
 * // => [1, 2, 3]
 *
 * // Curried with placeholders.
 * curried(1)(_, 3)(2)
 * // => [1, 2, 3]
 */
 function curry(func, arity) {
   const result = createWrap(func, WRAP_CURRY_FLAG, undefined, undefined, undefined, undefined, undefined, arity)
   result.placeholder = curry.placeholder
   return result
 }

 // Assign default placeholders.
 curry.placeholder = {}
```


#### Curring函数实现

思路就是当传入的参数个数没有到达func函数要求的参数个数的时候一直返回柯里化函数，只有参数满足func函数的个数的时候才通过apply执行func函数。

以下代码仅供参考：

```
function curry(func , thisArg){
  if ( !Array.isArray(thisArg) ) {
    thisArg = [];
  }
  return function(){
    let args = Array.prototype.slice.call(arguments);
    if ( (args.length+thisArg.length) < func.length ) {
      return curry(func , thisArg.concat(args));
    }
    return func.apply(this , thisArg.concat(args));
  };
}

// 调用Curring函数
var add = curry(function(x,y){      
  return x+y;
});

var increment = add(1);
increment(2);  // 3
```

Curring函数就是保证参数不够的时候一直返回一个函数，用来接受参数， 当执行函数时传入的参数个数（参数是累加的，也就是说上一次的参数会传入到下一个函数）满足条件时再执行真正要执行的函数。

### 反柯里化(uncurrying)

从字面上看，它的作用好像是和柯里化的作用相反，事实上两者之间并没有存在这种关系。
Uncurrying的话题来自JavaScript之父 Brendan Eich在2011年发表的一篇推特，它解决的问题是让对象去借用一个原本不属于自己的方法。

下面是通过反柯里化函数，把原型对象的函数转化为一个普通的函数，实现相同的功能，代码如下：

```
Function.prototype.uncurrying = function() {
  var self = this;
  return function() {
    return Function.prototype.call.apply(self, arguments);
  };
};

var slice = Array.prototype.slice.uncurrying();
var a = [1,2,3];
console.log(slice(a,1)); // [2,3]
```


### 性能
Curring柯里化肯定会有一些开销（函数嵌套，比普通函数占更多内存），从另外一个角度分析，不管你用不用柯里化这个思维，你的代码很可能已经步入了更复杂的模式，会有更大的开销。

影响性能的点：  
1. 存取 arguments 对象通常要比存取命名参数要慢一些；  
2. 一些老版本的浏览器在 arguments.length 的实现上相当慢；  
3. 使用 fn.apply() 和 fn.call() 要比直接调用 fn() 要慢点；  
4. 创建大量嵌套作用域和闭包会带来开销。  


### 总结

函数柯里化给我们带来的是：解决问题的一种逻辑思维方式。
