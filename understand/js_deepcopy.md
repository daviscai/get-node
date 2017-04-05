## 理解深复制和浅复制

深复制和浅复制涉及到复制`值和引用`，ECMAScript 标准定义了 7 种数据类型:
```
Boolean
Null
Undefined
Number
String
Object
Symbol (ECMAScript 6 新定义)
```

其中，Boolean，Number，String属于原始类型，是值传递，值本身不可改变，那么复制它们就是直接复制值，比较容易理解。

而对象称为合成类型（complex type），Object类型是引用传递，保存的是内存地址，值是可变的，那么复制它们就复杂一些。

数组是一种使用整数作为键属性和长度属性之间关联的常规对象，数组也属于对象类型
```
typeof window // "object"
typeof {} // "object"
typeof [] // "object"
typeof new Set() // "object"
typeof new Map() // "object"
```

简单来说，浅复制只复制一层对象的属性，而深复制则递归复制了所有层级的属性。

### 浅复制 shadow copy

直接看代码

```
function shadowCopy(src) {
  var dst = {};
  for (var prop in src) {
      dst[prop] = src[prop];  //只复制一层属性
  }
  return dst;
}

var obj = { a:1, arr: [2,3] };
var shadowObj = shadowCopy(obj);
console.log(shadowObj)
```

输出：
```
Object {a: 1, arr: Array(2)}
```

由于Object对象类型保存的是内存地址，复制的只是内存地址编号，实际上是指向同一块内存，如上面例子会导致obj.arr和shadowObj.arr指向同一块内存地址，如果我们改变了 shadowObj.arr的值，原对象obj.arr值也跟着改变，因为改变了内存里的值。

```
shadowObj.arr[1] = 5;
obj.arr[1]   // = 5
```

### 深复制 deep copy

深复制不仅将原对象的各个属性逐个复制出去，而且还将原对象各个属性所包含的对象也依次采用深复制的方法递归复制到新对象上，如果是对象类型，把内存里的值也复制出来，不只是复制地址。

深复制后的对象和原对象互不影响，完全独立，不存在访问同一块内存的问题。


### 实现深复制的方法

#### 1. jQuery.extend  
jQuery.extend第一个参数可以是布尔值，用来设置是否深度拷贝的:   
jQuery.extend(true, { a : { a : "a" } }, { a : { b : "b" } } );  


#### 2. JSON.parse 和 JSON.stringify
```
var result = JSON.parse(JSON.stringify(test))
```

不过有局限性：  
1. 无法复制函数  
2. 原型链没了，对象就是object，所属的类没了。   

针对纯 JSON 数据对象的深复制，使用 JSON 全局对象的 parse 和 stringify 方法来实现深复制也算是一个简单讨巧的方法。然而它能正确处理的对象只有 Number, String, Boolean, Array, 扁平对象，即那些能够被 json 直接表示的数据结构。


#### 3. 递归复制实现深拷贝

```
// 递归实现一个深拷贝
function deepClone(source){
   if(!source && typeof source !== 'object'){
     return source;
   }
   var targetObj = source.constructor === Array ? [] : {};
   for(var keys in source){
      if(source.hasOwnProperty(keys)){
         if(source[keys] && typeof source[keys] === 'object'){
           targetObj[keys] = source[keys].constructor === Array ? [] : {};
           // 递归
           targetObj[keys] = deepClone(source[keys]);
         }else{
           targetObj[keys] = source[keys];
         }
      }
   }
   return targetObj;
}

// test example
var o1 = {
  arr: [1, 2, 3],
  obj: {
    key: 'value'
  },
  func: function(){
    return 1;
  }
};

var o3 = deepClone(o1);
console.log(o3 === o1); // => false
console.log(o3.obj === o1.obj); // => false
console.log(o3.func === o1.func); // => true
```


### 延伸阅读：
[《深入剖析 JavaScript 的深复制》](http://jerryzou.com/posts/dive-into-deep-clone-in-javascript/)
