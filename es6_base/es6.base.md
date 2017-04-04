title: ECMAscript2015 介绍
speaker: @蚊子
url:
transition: slide3
files:
theme: dark
usemathjax: yes
date: 2016.10.21

[slide]
## ES6/ECMAScript2015 介绍
##### 不一样的Javascript
----
###### @蚊子

[slide]
## 前世今生
----
* ECMA(国际标准化组织)制定语言标准(规范)ECMAScript，而Javascript是具体的实现 {:&.zoomIn}
* 1997年发布`ECMAScript1.0`, 1998年发布`ECMAScript2.0`, 1999年发布`ECMAScript3.0`
* 2007 计划中的`ECMAScript4.0` 流产，原因是不向下兼容，语法和新特性都太过激进，遭到各大浏览器厂商的抵制
* 2009年，对ECMAScript 4.0部分功能进行改善，发布`ECMAScript5.0`, 项目代号为harmony(和谐)，即`ES5`
* 2015年发布ECMAScript6，也称为`ECMAScript2015`，也称`ES6`.

[slide]
## 任何人的建议都有可能会成为正式标准
----
* Stage 0: Strawman 展示阶段
* Stage 1: Proposal 征求意见阶段
* Stage 2: Draft 草案阶段
* Stage 3: Candidate 候选阶段
* Stage 4: Finished 定案阶段

[TC39](https://github.com/tc39)

[slide]
## ECMAscript 是语言标准规范，实现者有:
----
* Javascript {:&.rollIn}
* TypeScript , 静态语言强类型支持
* CoffeeScript
* Babel （非语言，转换编译器）
* Node.js (非语言，V8), [node.green](http://node.green/)
* 各大浏览器厂商

[slide]

[magic data-transition="move"]
## 基础
----
* 块级作用域
* 变量的解构赋值
* 字符串的扩展
* 正则的扩展
* Math对象的扩展
* Number对象的扩展
* 数组的扩展
* 扩展运算符
* 函数绑定
* 箭头函数
* for...of 循环
* Set & Map

====

## 高阶
----
* Iterator迭代器
* generator函数
* promise
* async异步函数
* class
* 模块化
* proxy函数劫持
* Reflect
* Symbol

[/magic]

[slide]

[magic data-transition="move"]
## 块级作用域
<pre><code class="javascript">
{
    let a = 10;
    var b = 20;
}

console.log(a); // ReferenceError: a is not defined
console.log(b); // 20

</code></pre>
====
## 为什么需要块级作用域
----
* 会发生变量提升，内层变量覆盖外层变量 {:&.rollIn}
    <pre><code class="javascript">
    var tmp = new Date();
    function f(){
	    console.log(tmp);
	    if(false) {
		    var tmp = 'hello world';
	    }
    }
    f();  // undefined
    </code></pre>

* 用来计数的临时变量意外地成为全局变量
  <pre><code class="javascript">
    var str = 'hello';
    for(var i=0; i < str.length; i++) {
	    console.log(str[i]);
    }
    console.log(i);  // 5
    </code></pre>
====
## ES6的块级作用域
----
* let 或 const 声明的变量，其作用域为块级作用域 {:&.rollIn}
* 由let，const声明的全局变量不属于全局对象的属性(window/global)
* 在相同作用域下不能重复声明
* 先声明后使用，不存在变量提升
  <pre><code class="javascript">
	console.log(tmp); //ReferenError
	let tmp = 10;
  </code></pre>
* 总之，在ES6里尽量用`let`代替`var`来声明变量
* 减少运行时错误，避免变量在声明前被使用
[/magic]

[slide]
[magic data-transition="move"]
## let 命令
----
* typeof 不再是100%可用 {:&.zoomIn}
  <pre><code class="javascript">
  typeof x; // ReferenError
  let x;   
  </code></pre>
* 小心函数参数变量的声明顺序
  <pre><code class="javascript">
  function bar( x=y, y=2){
   	return [x,y];
  }
  bar(); //报错 ReferenceError: y is not defined
  </code></pre>

====

* 下面哪个会报错
  <pre><code class="javascript">
  function bar1(arg){
   	let arg = 0;
  }

  function bar2(arg){
	{
   	let arg = 0;
	}
  }

  bar1();
  bar2();
  </code></pre>

[/magic]

[slide]
[magic data-transition="move"]
## const 命令
----
* const 用来声明常量，声明后必须初始化赋值，一旦声明，其值就不能改变 {:&.zoomIn}
* const 声明的变量是块级作用域，同样具有let声明变量的特性
	* 块级作用域内有效
	* 不能重复声明
	* 不存在变量提升，必须先声明后使用
* const 声明的对象，是指向对象的地址，声明后不能改变地址，但对象的值可以添加
  <pre><code class="javascript">
  const foo = {};
  foo.age = 10;
  console.log(foo.age); // 10
  foo = {}; // TypeError: "foo" is read-only
  </code></pre>

====

## 跨模块常量
----
  <pre><code class="javascript">
  // abc.js
  export const X = 1;
  export const Y = 2;

  //test1.js
  import * as a from './abc';
  console.log(a.X);  // 1
  console.log(a.Y);  // 2

  //test2.js
  import {X,Y} from './abc';
  console.log(X);  // 1
  console.log(Y);  // 2

  </code></pre>
[/magic]


[slide]

[magic data-transition="move"]
## 变量的解构赋值
----
* ES6允许按照一定模式，从`数组`和`对象`中提取值，对变量进行赋值，这被称为解构（Destructuring） {:&.zoomIn}
  <pre><code class="javascript">
  let [a,b,c] = [1,2,3];
  </code></pre>

* 如果解构不成功，变量的值就等于undefined
  <pre><code class="javascript">
  var [foo] = [];
  var [bar, foo] = [1];
  </code></pre>

====

### 本质上，这种写法属于`“模式匹配”`
----
  <pre><code class="javascript">
  let [foo, [[bar], baz]] = [1, [[2], 3]];
  foo // 1
  bar // 2
  baz // 3

  let [ , , third] = ["foo", "bar", "baz"];
  third // "baz"

  let [x, , y] = [1, 2, 3];
  x // 1
  y // 3

  let [head, ...tail] = [1, 2, 3, 4];
  head // 1
  tail // [2, 3, 4]

  let [x, y, ...z] = ['a'];
  x // "a"
  y // undefined
  z // []`
  </code></pre>

====

### 另一种情况是不完全解构
#### 即等号左边的模式，只匹配一部分的等号右边的数组
----
  <pre><code class="javascript">
  let [x, y] = [1, 2, 3];
  x // 1
  y // 2

  let [a, [b], d] = [1, [2, 3], 4];
  a // 1
  b // 2
  d // 4
  </code></pre>

====

### 解构赋值允许指定默认值
----
  <pre><code class="javascript">
  var [foo = true] = [];
  foo // true

  function f() {
      console.log('aaa');
  }
  let [x = f()] = [1];

  let [x = 1, y = x] = [];     // x=1; y=1
  let [x = 1, y = x] = [2];    // x=2; y=2
  let [x = 1, y = x] = [1, 2]; // x=1; y=2

  </code></pre>

====
### 对象的解构赋值
----
* 变量必须与属性同名，才能取到正确的值 {:&.zoomIn}
  <pre><code class="javascript">
  var { foo, bar } = { foo: "aaa", bar: "bbb" };
  foo // "aaa"
  bar // "bbb"
  </code></pre>

* <pre><code class="javascript">
  var { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
  </code></pre>
* 也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量
  <pre><code class="javascript">
  var { foo: baz } = { foo: "aaa", bar: "bbb" };
  baz // "aaa"
  foo // error: foo is not defined
  </code></pre>

====
### 对象的解构也可以指定默认值
----
  <pre><code class="javascript">
  var {x = 3} = {};
  x // 3

  var {x, y = 5} = {x: 1};
  x // 1
  y // 5

  var {x:y = 3} = {};
  y // 3

  var {x:y = 3} = {x: 5};
  y // 5

  var { message: msg = 'Something went wrong' } = {};
  msg // "Something went wrong"
  </code></pre>

====
### 字符串、数值、布尔值的解构赋值
----
* 字符串的解构赋值
  <pre><code class="javascript">
  const [a, b, c, d, e] = 'hello';
  a // "h"
  b // "e"
  c // "l"
  d // "l"
  e // "o"
  </code></pre>

* 数值和布尔值的解构赋值
  <pre><code class="javascript">
  let {toString: s} = 123;
  s === Number.prototype.toString // true

  let {toString: s} = true;
  s === Boolean.prototype.toString // true
  </code></pre>

====
### 解构赋值的用途
----
* 交换变量的值变得更加方便： [x, y] = [y, x];
* 从函数返回多个值时，赋值更加方便
  <pre><code class="javascript">
  function example() {
     return [1, 2, 3];
  }
  var [a, b, c] = example();
  </code></pre>
* 函数参数的定义更加灵活
  <pre><code class="javascript">
  // 参数是一组有次序的值
  function f([x, y, z]) { ... }
  f([1, 2, 3]);

  // 参数是一组无次序的值
  function f({x, y, z}) { ... }
  f({z: 3, y: 2, x: 1});
  </code></pre>

====
* 提取JSON数据
  <pre><code class="javascript">
  var jsonData = {
    id: 42,
    status: "OK",
    data: [867, 5309]
  };

  let { id, status, data: number } = jsonData;

  console.log(id, status, number);
  // 42, "OK", [867, 5309]
  </code></pre>

* 输入模块的指定方法更加清晰
  <pre><code class="javascript">
  const { SourceMapConsumer, SourceNode } = require("source-map");
  </code></pre>

====
* 遍历Map结构更加方便
  <pre><code class="javascript">
  var map = new Map();
  map.set('first', 'hello');
  map.set('second', 'world');

  for (let [key, value] of map) {
    console.log(key + " is " + value);
  }
  // first is hello
  // second is world
  </code></pre>

[/magic]

[slide]
[magic data-transition="move"]
## 字符串的扩展
====
### ES6为字符串添加了遍历器(Iterator)接口
### 字符串可以使用 for ... of 遍历
----
  <pre><code class="javascript">
  for (let codePoint of 'foo') {
      console.log(codePoint)
  }
  // "f"
  // "o"
  // "o"
  </code></pre>

优点是可以识别大于0xFFFF的字符编码，比如中文: 𠮷

====
### 新增 includes(), startsWith(), endsWith()
----
* 传统上，JavaScript只有indexOf方法, 返回`下标位置` {:&.zoomIn}
* ES6又提供了三种新方法。
  * includes()：返回`布尔值`，表示是否找到了参数字符串。
  * startsWith()：返回`布尔值`，表示参数字符串是否在源字符串的头部。
  * endsWith()：返回`布尔值`，表示参数字符串是否在源字符串的尾部。

  <pre><code class="javascript">
  var s = 'Hello world!';

  s.startsWith('Hello') // true
  s.endsWith('!') // true
  s.includes('o') // true

  s.startsWith('world', 6) // true
  s.endsWith('Hello', 5) // true, 等同于 s.startsWith('Hello',0); 意思是前5个字符是否以"Hello"结束
  s.includes('Hello', 6) // false
  </code></pre>

====
### 新增 repeat()
----
* repeat方法返回一个新字符串，表示将原字符串重复n次。
* <pre><code class="javascript">
  'x'.repeat(3) // "xxx"
  'hello'.repeat(2) // "hellohello"
  'na'.repeat(0) // ""
  </code></pre>
* 参数如果是小数，会被取整。
  <pre><code class="javascript">
  'na'.repeat(2.9) // "nana"
  </code></pre>
* 参数不能为负数，否则报错

====
### 新增 padStart()，padEnd()
----
* `ES7`推出了字符串补全长度的功能 {:&.zoomIn}
* padStart用于头部补全，padEnd用于尾部补全
  <pre><code class="javascript">
  'x'.padStart(5, 'ab') // 'ababx'
  'x'.padStart(4, 'ab') // 'abax'

  'x'.padEnd(5, 'ab') // 'xabab'
  'x'.padEnd(4, 'ab') // 'xaba'
  </code></pre>
* 如果省略第二个参数，则会用空格补全长度
  <pre><code class="javascript">
  'x'.padStart(4) // '   x'
  'x'.padEnd(4) // 'x   '
  </code></pre>
====
### padStart 常见用途
----
* 为数值补全指定位数
  <pre><code class="javascript">
  '1'.padStart(10, '0')      // "0000000001"
  '12'.padStart(10, '0')     // "0000000012"
  '123456'.padStart(10, '0') // "0000123456"
  </code></pre>

* 提示字符串格式化
  <pre><code class="javascript">
  '12'.padStart(10, 'YYYY-MM-DD')    // "YYYY-MM-12"
  '09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
  </code></pre>

====
### 模板字符串
----
* 终于可以不用拼接字符串了 {:&.zoomIn}
* 传统的JavaScript语言，输出模板通常是这样写的
  <pre><code class="javascript">
  $('#result').append(
      'There are <b>' + basket.count + '</b> ' +
      'items in your basket, ' +
      '<em>' + basket.onSale +
      '</em> are on sale!'
  );
  </code></pre>
* ES6引入了模板字符串
  <pre><code class="javascript">
  $('#result').append(`
      There are <b>${basket.count}</b> items
      in your basket, <em>${basket.onSale}</em>
      are on sale!
  `);
  </code></pre>

====
### 模板字符串可以用来干这些
----
* 表示多行字符串 {:&.zoomIn}
* 模板字符串中嵌入变量
  <pre><code class="javascript">
  // 普通字符串
  `In JavaScript '\n' is a line-feed.`

  // 多行字符串
  `In JavaScript this is
   not legal.`

  console.log(`string text line 1
  string text line 2`);

  // 字符串中嵌入变量
  var name = "Bob", time = "today";
  `Hello ${name}, how are you ${time}?`
  </code></pre>

====
* 可以放入任意的JavaScript表达式
* 模板字符串之中还能调用函数
  <pre><code class="javascript">
  var x = 1;
  var y = 2;

  `${x} + ${y} = ${x + y}`  // "1 + 2 = 3"

  function fn() {
    return "Hello World";
  }
  `foo ${fn()} bar`  // foo Hello World bar

  </code></pre>

====
* 模板字符串甚至还能嵌套
  <pre><code class="javascript">
  const tmpl = addrs => `
  Hello ${name}
      ${addrs.map(addr => `
          aaaaa, ${addr.first}
      `).join('')}
  `;
  </code></pre>

====
### `不建议`用模板字符串干这些
* 放入JavaScript表达式
* 模板字符串里调用函数
* 模板字符串多层嵌套


[/magic]

[slide]
[magic data-transition="move"]
## 正则的扩展
#### 该知识点用得较少，了解即可
====
### RegExp构造函数
----
* ES5 的写法：  {:&.zoomIn}
  ```js
  var regex = new RegExp('xyz', 'i');
  var regex = new RegExp(/xyz/i);
  // 等价于
  var regex = /xyz/i;
  ```
* ES6写法：  
  ```js
  var regex = new RegExp(/abc/ig, 'i')
  ```
  原有正则对象的修饰符是ig，它会被第二个参数i覆盖

====
### u修饰符
----
* ES6对正则表达式添加了u修饰符，含义为“Unicode模式” {:&.zoomIn}
* `/^\uD83D/u.test('\uD83D\uDC2A')`  // false
* 对于码点大于0xFFFF的Unicode字符，点字符不能识别，必须加上u修饰符
  ```js
  var s = '𠮷';
  /^.$/.test(s) // false
  /^.$/u.test(s) // true
  ```
* 使用u修饰符后，所有量词都会正确识别码点大于0xFFFF的Unicode字符。
  ```js
  /a{2}/.test('aa') // true
  /a{2}/u.test('aa') // true
  /𠮷{2}/.test('𠮷𠮷') // false
  /𠮷{2}/u.test('𠮷𠮷') // true
  ```

====
### y修饰符
----
* ES6还为正则表达式添加了y修饰符 {:&.zoomIn}
* y修饰符的作用与g修饰符类似，也是全局匹配, 不同的是:  
  `y修饰符确保匹配必须从剩余的第一个位置开始`
* ```js
  var s = 'aaa_aa_a';
  var r = /a+_/y;

  r.exec(s) // ["aaa_"]
  r.exec(s) // ["aa_"]
  ```

[/magic]

[slide]
[magic data-transition="move"]
## Math对象的扩展
====
### ES6在Math对象上新增了`17`个与数学相关的方法
----
* Math.trunc()  : 用于去除一个数的小数部分，返回整数部分 {:&.zoomIn}
* Math.sign() : 用来判断一个数到底是正数、负数、还是零
* ES7新增了一个指数运算符（\*\*），目前Babel转码器已经支持。
  ```js
  2 ** 2 // 4
  2 ** 3 // 8

  let a = 2;
  a **= 2;
  // 等同于 a = a * a;
  ```
* 其他Math方法可以在需要时再研究，不做介绍

[/magic]

[slide]
[magic data-transition="move"]
## Number对象的扩展
====

### 二进制和八进制表示法
----
* ES6提供了二进制和八进制数值的新的写法，分别用前缀0b（或0B）和0o（或0O）表示  {:&.zoomIn}
* 从ES5开始，在严格模式之中，八进制就不再允许使用前缀0表示。
* ```js
  // 非严格模式
  (function(){
     console.log(0o11 === 011);
  })() // true
  ```
====
### Number.isFinite() 和 Number.isNaN()
----
* Number.isFinite() : 用来检查一个数值是否为有限的（finite） {:&.zoomIn}
  ```js
  Number.isFinite(15); // true
  Number.isFinite(0.8); // true
  Number.isFinite(NaN); // false
  ```
* Number.isNaN() : 用来检查一个值是否为NaN
  ```js
  Number.isNaN(NaN) // true
  Number.isNaN(15) // false
  Number.isNaN('15') // false
  ```
* 与传统的全局方法isFinite()和isNaN()的区别:  
  新方法只对数值有效，非数值一律返回false

====
### Number.parseInt(), Number.parseFloat()  
----
* ES6将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。 {:&.zoomIn}
  ```js
  // ES5的写法
  parseInt('12.34') // 12
  parseFloat('123.45#') // 123.45

  // ES6的写法
  Number.parseInt('12.34') // 12
  Number.parseFloat('123.45#') // 123.45
  ```
* 这样做的目的，是逐步减少全局性方法，使得语言逐步模块化

[/magic]

[slide]
[magic data-transition="move"]
## 数组的扩展
====

### ES6对数组的扩展极大的增强了操作数组的便利性
----
* Array.from() : 将对象、字符串转换为数组，object2array  {:&.zoomIn}
* ```js
  let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
  };

  // ES5的写法
  var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

  // ES6的写法
  let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']

  Array.from('hello')
  // ['h', 'e', 'l', 'l', 'o']

  let namesSet = new Set(['a', 'b'])
  Array.from(namesSet) // ['a', 'b']
  ```
====
### Array.of()  
----
* 用于将一组值，转换为数组, 弥补数组构造函数Array()的不足 {:&.zoomIn}
* ```js   
  Array() // []
  Array(3) // [, , ,]
  Array(3, 11, 8) // [3, 11, 8]
  ```
* Array.of 总是返回参数值组成的数组，可替代Array()或new Array()
  ```js
  Array.of() // []
  Array.of(undefined) // [undefined]
  Array.of(1) // [1]
  Array.of(1, 2) // [1, 2]
  ```

====
### 数组实例的copyWithin()
----
* 在当前数组内部，将指定位置的成员复制到其他位置  {:&.zoomIn}
* Array.prototype.copyWithin(target, start = 0, end = this.length)
* ```js
  //表示将从3号位直到数组结束的成员（4和5），复制到从0号位开始的位置，结果覆盖了原来的1和2。
  [1, 2, 3, 4, 5].copyWithin(0, 3)
  // [4, 5, 3, 4, 5]

  // 将3号位复制到0号位
  [1, 2, 3, 4, 5].copyWithin(0, 3, 4)
  // [4, 2, 3, 4, 5]
  ```

====

###  数组实例的find()和findIndex()
----
* find()方法用于找出第一个符合条件的数组成员  {:&.zoomIn}
* 它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员
* 如果没有符合条件的成员，则返回undefined
* ```js
  //找出数组中第一个小于0的成员
  [1, 4, -5, 10].find( function(n){
	return n < 0;
  });
  // -5
```
* findIndex() 方法是找出第一个符合条件的数组成员`位置`，用法跟find()方法一样。

====

### 数组实例的entries()，keys()和values()
----
* ES6提供三个新的方法：entries()，keys()和values()——用于遍历数组 {:&.zoomIn}
* keys()是对`键名`的遍历，values()是对`键值`的遍历，entries()是对`键值对`的遍历
* ```js
  for (let index of ['a', 'b'].keys()) {
    console.log(index);
  }
  // 0
  // 1

  for (let elem of ['a', 'b'].values()) {
    console.log(elem);
  }
  // 'a'
  // 'b'

  for (let [index, elem] of ['a', 'b'].entries()) {
    console.log(index, elem);
  }
  ```

====

### 数组实例的includes()
----
* 表示某个数组是否包含给定的值，与字符串的includes方法类似 {:&.zoomIn}
* ```js
  [1, 2, 3].includes(2);     // true
  [1, 2, 3].includes(4);     // false
  [1, 2, NaN].includes(NaN); // true
  ```

[/magic]

[slide]
[magic data-transition="move"]
## 函数
====
### 更强大，更简洁的函数
----
* 支持默认参数，加强了对参数的处理能力 {:&.zoomIn}
* 支持Rest参数，可扩展参数
* 箭头函数
* 绑定this
* 尾调用优化

====
### 函数的默认参数
----
* 在ES6之前  {:&.zoomIn}
  ```js
  function log(x, y) {
    y = y || 'World';
    console.log(x, y);
  }
  log('Hello') // Hello World
  log('Hello', 'China') // Hello China
  ```
* ES6支持默认参数
  ```js
  function log(x, y = 'World') {
    console.log(x, y);
  }
  log('Hello') // Hello World
  log('Hello', 'China') // Hello China
  ```
* 参数变量是默认声明的，所以不能用let或const再次声明
  ```js
  function foo(x = 5) {
    let x = 1; // error
  }
  ```
====
### 默认参数与解构赋值结合
----
* 对象的解构赋值作为参数  {:&.zoomIn}
  ```js
  function foo({x, y = 5}) {
    console.log(x, y);
  }

  foo({}) // undefined, 5
  foo({x: 1}) // 1, 5
  foo({x: 1, y: 2}) // 1, 2
  foo() // TypeError: Cannot read property 'x' of undefined
  ```
*  双重默认值
  ```js
  function fetch(url, { method = 'GET' } = {}) {
    console.log(method);
  }

  fetch('http://example.com')
  // "GET"
  ```

====
### 默认参数的作用域
----
* 参数默认值是变量，其作用域在先函数，后全局 {:&.zoomIn}
  ```js
  var x = 1;

  function f(x, y = x) {
    console.log(y);
  }

  f(2) // 2
  f()  // 1
  ```
* 参数的默认值是一个函数, 该函数的作用域是其声明时所在的作用域
  ```js
  let foo = 'outer';

  function bar(function(x){
	return foo;
  }) {
    let foo = 'inner';
    console.log(func()); // outer
  }

  bar();
  ```

====
### Rest参数
----
* ES6引入rest参数（形式为“...变量名”），用于获取函数的多余参数 {:&.zoomIn}
* 例如，利用rest参数，可以向add函数传入任意数目的参数
  ```js
  function add(...values) {
    let sum = 0;
    for (var val of values) {
      sum += val;
    }
    return sum;
  }

  add(2, 5, 3) // 10
  ```
* rest参数，实参不是数组，而是以逗号分隔的参数序列，形参在函数体内是数组
* rest参数之后不能再有其他参数（即只能是最后一个参数）

====
### 扩展运算符
----
* 扩展运算符是三个点（...x） {:&.zoomIn}
* 它好比rest参数的逆运算，将一个数组转为用逗号分隔的参数序列
* 主要用于函数调用，传递参数
  ```js
  function add(x, y) {
    return x + y;
  }

  var numbers = [4, 38];
  add(...numbers) // 42
  // add(4,38);
  ```
* 扩展运算符用于实参，rest参数用于形参，尽管看起来一样，本质不同

====
### 扩展运算符的应用
----
* 合并数组
  ```js
  // ES5
  [1, 2].concat(more)
  // ES6
  [1, 2, ...more]

  var arr1 = ['a', 'b'];
  var arr2 = ['c'];
  var arr3 = ['d', 'e'];

  // ES5的合并数组
  arr1.concat(arr2, arr3);
  // [ 'a', 'b', 'c', 'd', 'e' ]

  // ES6的合并数组
  [...arr1, ...arr2, ...arr3]
  // [ 'a', 'b', 'c', 'd', 'e' ]
  ```
* 与解构赋值结合
  ```js
  const [first, ...rest] = [1, 2, 3, 4, 5];
  first // 1
  rest  // [2, 3, 4, 5]
  ```

====
* 扩展运算符还可以将字符串转为真正的数组
  ```js
  [...'hello']
  // [ "h", "e", "l", "l", "o" ]
  ```
* 任何Iterator接口的对象，都可以用扩展运算符转为真正的数组
  ```js
  // Map和Set结构，Generator函数
  let map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three'],
  ]);

  let arr = [...map.keys()]; // [1, 2, 3]
  ```
* ...x 和 [...x] 的区别

====
### 箭头函数
----
* ES6允许使用“箭头”（=>）定义函数 {:&.zoomIn}
* 比如：
  ```js
  var f = v => v;
  //等同于：
  var f = function(v) {
    return v;
  };

  var f = () => 5;
  // 等同于
  var f = function () { return 5 };

  var sum = (num1, num2) => num1 + num2;
  // 等同于
  var sum = function(num1, num2) {
    return num1 + num2;
  };
  ```
====
### 箭头函数有几个使用注意点
* 函数体内的this对象，是定义时所在的对象，而不是使用时所在的对象
  ```js
  function foo() {
    setTimeout(() => {
      console.log('id:', this.id);
    }, 100);
  }

  var id = 21;

  foo.call({ id: 42 });
  // id: 42
  ```
====
* 箭头函数可以让this指向固定化，这种特性很有利于封装回调函数
  ```js
  // ES6
  function foo() {
    setTimeout(() => {
      console.log('id:', this.id);
    }, 100);
  }

  // ES5
  function foo() {
    var _this = this;

    setTimeout(function () {
      console.log('id:', _this.id);
    }, 100);
  }
  ```
* 箭头函数没有自己的this.

====
* 不可以当作构造函数，也就是说，不可以使用new命令
* 不可以使用arguments对象，该对象在函数体内不存在
* 不可以使用yield命令，因此箭头函数不能用作Generator函数
* 可读性和简洁之间的平衡，可读性优先，可看: [ES6的箭头函数真那么好用吗](http://igeekbar.com/igeekbar/post/104.htm)

====
### 什么是尾调用?
----
* 尾调用（Tail Call）是函数式编程的一个重要概念，就是指某个函数的最后一步是调用另一个函数。 {:&.zoomIn}
* 函数f的最后一步是调用函数g，这就叫尾调用
  ```js
  function f(x){
    return g(x);
  }
  ```
* 下面不属于尾调用
  ```js
  // 情况一
  function f(x){
    let y = g(x);
    return y;
  }

  // 情况二
  function f(x){
    return g(x) + 1;
  }

  // 情况三
  function f(x){
    g(x);
    // return undefined;
  }
  ```
====
### 尾调用优化原因和意义
----
* 函数调用会在内存形成一个“调用记录”，又称“调用帧”，保存调用位置和内部变量等信息 {:&.zoomIn}
* 尾调用作为函数内最后一步操作，无需保存位置等信息，可以直接返回，以节省内存
* ```js
  function f() {
    let m = 1;
    let n = 2;
    return g(m + n);
  }
  f();

  // 等同于
  function f() {
    return g(3);
  }
  f();

  // 等同于
  g(3);
  ```

====
### 尾递归
----
* 函数调用自身，称为递归。如果尾调用自身，就称为尾递归 {:&.zoomIn}
* 普通递归，计算n的阶乘，最多需要保存n个调用记录，复杂度 O(n)
  ```js
  function factorial(n) {
    if (n === 1) return 1;
    return n * factorial(n - 1);
  }

  factorial(5) // 120
  ```
* 尾递归，只保留一个调用记录，复杂度 O(1)
  ```js
  function factorial(n, total) {
    if (n === 1) return total;
    return factorial(n - 1, n * total);
  }
  factorial(5, 1) // 120
  ```
* 对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误
* ES6明确规定，所有ECMAScript的实现，都必须部署“尾调用优化”

====
### 柯里化（currying）
----
* 函数式编程有一个概念，叫做柯里化（currying），意思是将多参数的函数转换成单参数的形式 {:&.zoomIn}
* 只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数
* ```js
  function currying(fn, n) {
    return function (m) {
      return fn.call(this, m, n);
    };
  }
  function tailFactorial(n, total) {
    if (n === 1) return total;
    return tailFactorial(n - 1, n * total);
  }
  const factorial = currying(tailFactorial, 1);
  factorial(5); //只传了一个参数
  ```
* 更多请看 [柯里化（curry）](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch4.html#总结)

[/magic]

[slide]
[magic data-transition="move"]
## 对象的扩展
====
### 更简洁的属性表示法
----
* ES6允许直接写入变量和函数，作为对象的属性和方法 {:&.zoomIn}
* ```js
  var foo = 'bar';
  var baz = {foo};
  baz // {foo: "bar"}

  // 等同于
  var baz = {foo: foo};
  ```
* 属性名为变量名, 属性值为变量的值
* ```js
  function f(x, y) {
    return {x, y};
  }
  // 等同于
  function f(x, y) {
    return {x: x, y: y};
  }
  ```

====
* 方法也可以简写
  ```js
  var o = {
    method() {
      return "Hello!";
    }
  };

  // 等同于
  var o = {
    method: function() {
      return "Hello!";
    }
  };
  ```
* CommonJS模块输出变量，就非常适合使用简洁写法
* ```js
  module.exports = { getItem, setItem, clear };
  // 等同于
  module.exports = {
    getItem: getItem,
    setItem: setItem,
    clear: clear
  };
  ```

====
### 支持表达式作为属性名
----
* 在 ES5 中只能使用标识符定义属性  {:&.zoomIn}
  ```js
  var obj = {
    foo: true,
    abc: 123
  };
  ```

* ES6 允许用表达式作为对象的属性名  
  ```js
  let propKey = 'foo';
  let obj = {
    \[propKey\]: true
  };
  ```

====
### Object.is
----
* Object.is 比较两个值是否相等 {:&.zoomIn}
  ```js
  +0 === -0 //true
  NaN === NaN // false

  Object.is(+0, -0) // false
  Object.is(NaN, NaN) // true
  ```

====
#### Object.assign
----
* Object.assign 用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target） {:&.zoomIn}
* ```js
  var target = { a: 1 };

  var source1 = { b: 2 };
  var source2 = { c: 3 };

  Object.assign(target, source1, source2);
  target // {a:1, b:2, c:3}
  ```
* 方法的第一个参数是目标对象，后面的参数都是源对象
* 如果目标对象与源对象有同名属性，后面的属性会覆盖前面的属性
* Object.assign只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性，避免遍历某些不必要的属性
* Object.assign 是浅拷贝，而不是深拷贝，即只拷贝对象的引用。


====
### Object.assign 的用途
----
* 为对象添加属性  {:&.zoomIn}
  ```js
  class Point {
    constructor(x, y) {
      Object.assign(this, {x, y});
    }
  }
  ```
* 为对象添加方法
  ```js
  Object.assign(SomeClass.prototype, {
    someMethod(arg1, arg2) {
      ···
    }
  });

  // 等同于下面的写法
  SomeClass.prototype.someMethod = function (arg1, arg2) {
    ···
  };
  ```

====
* 克隆对象
  ```js
  function clone(origin) {
    return Object.assign({}, origin);
  }
  ```
* 合并多个对象
  ```js
  const merge = (target, ...sources) => Object.assign(target, ...sources);
  ```  

====
### Object.keys、Object.values、Object.entries
----
*  ES5引入了Object.keys方法，返回一个数组，成员是键名 {:&.zoomIn}

  ```js
  var obj = { foo: "bar", baz: 42 };
  Object.keys(obj)
  //["foo", "baz"]
  ```
* Object.values方法返回一个数组, 成员是键值
  ```js
  var obj = { foo: "bar", baz: 42 };
  Object.values(obj)
  //["bar", 42]
  ```
* Object.entries方法返回一个数组，成员是键值对数组
  ```js
  let obj = { one: 1, two: 2 };
  for (let [k, v] of Object.entries(obj)) {
    console.log(`${JSON.stringify(k)}: ${JSON.stringify(v)}`);
  }
  // "one": 1
  // "two": 2
  ```  

[/magic]


[slide]
[magic data-transition="move"]
## Set 数据结构

====
### 基本用法
----
* Set类似于数组，但是成员的值都是唯一的，没有重复的值 {:&.zoomIn}
  ```
  const s = new Set();
  [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
  for (let i of s) {
    console.log(i);
  }
  // 2 3 5 4
  ```
* Set初始化
  ```
  // 例一
  var set = new Set([1, 2, 3, 4, 4]);
  [...set]
  // [1, 2, 3, 4]

  // 例二
  var items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
  items.size // 5
  ```

====
* 向Set加入值的时候，不会发生类型转换，Set内部用 === 判断值是否相等
  ```
  1. 5 和 '5'是不同的值，类型不一样
  2. NaN 被认为是相同的值，只能存一个
  3. {} object被认为是不同的值，可以存多个
  ```
  ```
  let set = new Set();
  let a = NaN;
  let b = NaN;
  set.add(a);
  set.add(b);
  set // Set {NaN}

  let set = new Set();
  set.add({});
  set.size // 1

  set.add({});
  set.size // 2
  ```

====
### Set的操作方法
----
* add(value)：添加某个值，返回Set结构本身。
* delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
* has(value)：返回一个布尔值，表示该值是否为Set的成员。
* clear()：清除所有成员，没有返回值。
  ```
  s.add(1).add(2).add(2);
  // 注意2被加入了两次
  s.size // 2

  s.has(1) // true
  s.has(2) // true
  s.has(3) // false

  s.delete(2);
  s.has(2) // false
  ```

====
* Array.from方法可以将Set结构转为数组
  ```
  var items = new Set([1, 2, 3, 4, 5]);
  var array = Array.from(items);

  //去除数组重复成员
  function dedupe(array) {
    return Array.from(new Set(array));
  }
  dedupe([1, 1, 2, 3]) // [1, 2, 3]

  //或者用扩展运算符方式去除数组的重复成员
  function dedupe(array) {
    return [...new Set(array)];
  }

  ```
====
### Set的遍历操作
----
* keys()：返回键名的遍历器
* values()：返回键值的遍历器
* entries()：返回键值对的遍历器
* forEach()：使用回调函数遍历每个成员
* for...of循环遍历Set

====
* Set 结构没有键名，只有键值，所以keys方法和values方法的行为完全一致
  ```
  let set = new Set(['red', 'green', 'blue']);

  for (let item of set.keys()) {
    console.log(item);
  }

  for (let item of set.values()) {
    console.log(item);
  }

  for (let item of set.entries()) {
    console.log(item);
  }
  // ["red", "red"]
  // ["green", "green"]
  // ["blue", "blue"]

  for (let x of set) {
    console.log(x);
  }

  ```
====
### Set应用
----
* 扩展运算符（...）内部使用for...of循环，所以也可以用于Set结构 {:&.zoomIn}
  ```
  let set = new Set(['red', 'green', 'blue']);
  let arr = [...set];
  // ['red', 'green', 'blue']
  ```
* 数组的map和filter方法也可以用于Set
  ```
  let set = new Set([1, 2, 3]);
  set = new Set([...set].map(x => x * 2));
  // 返回Set结构：{2, 4, 6}

  let set = new Set([1, 2, 3, 4, 5]);
  set = new Set([...set].filter(x => (x % 2) == 0));
  // 返回Set结构：{2, 4}
  ```

====
### WeakSet 数据结构
----
* WeakSet的成员只能是对象，而不能是其他类型的值 {:&.zoomIn}
* WeakSet中的对象都是弱引用，即垃圾回收机制不考虑WeakSet对该对象的引用
* WeakSet有助于防止内存泄漏
* 只有三个方法可用：add(value)、has(value)、delete(value) ，没有size属性和遍历方法

[/magic]

[slide]
[magic data-transition="move"]
## Map 数据结构
====
#### 为什么需要Map数据结构
----
* 对象（Object），本质上是键值对的集合（Hash结构），只能用字符串当作键(局限性) {:&.zoomIn}
* Map数据结构类似于对象，也是键值对的集合，但键可以是各种数据类型，包括对象类型
* 如果需要“键值对”的数据结构，Map比Object更合适
  ```
  var m = new Map();
  var o = {p: 'Hello World'};

  m.set(o, 'content')
  m.get(o) // "content"

  m.has(o) // true
  m.delete(o) // true
  m.has(o) // false
  ```

====
### Map 构造函数
----
* Map也可以接受一个数组作为参数
  ```
  var map = new Map([
    ['name', '张三'],
    ['title', 'Author']
  ]);

  map.size // 2
  map.has('name') // true
  map.get('name') // "张三"
  map.has('title') // true
  map.get('title') // "Author"
  ```

====
### Map 使用要点
----
* 如果对同一个键多次赋值，后面的值将覆盖前面的值 {:&.zoomIn}
  ```
  let map = new Map();
  map
  .set(1, 'aaa')
  .set(1, 'bbb');
  map.get(1) // "bbb"
  ```
* 如果读取一个未知的键，则返回undefined
* 只有对同一个对象的引用，Map结构才将其视为同一个键
  ```
  var map = new Map();
  map.set(['a'], 555);
  map.get(['a']) // undefined, 内存地址不一样

  var map = new Map();
  var a = ['a'];
  map.set(a, 555);
  map.get(a) // 555
  ```

====
### Map 属性和方法
----
* size属性, 返回Map结构的成员总数  {:&.zoomIn}

* set(key, value) , 如果key已经有值，则值会被更新
  ```
  // set方法返回的是Map本身，因此可以采用链式写法
  let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');
  ```
* get(key) , 如果找不到key，返回undefined
  ```
  var m = new Map();
  var hello = function() {console.log("hello");}
  m.set(hello, "Hello ES6!") // 键是函数
  m.get(hello)  // Hello ES6!
  ```
* has(key) , delete(key) , clear()

====
### Map 遍历方法
----
* keys()：返回键名的遍历器
* values()：返回键值的遍历器
* entries()：返回所有成员的遍历器
* forEach()：遍历Map的所有成员
* 特别注意的是，Map的遍历顺序就是插入顺序

====
### Map 遍历方法示例
----
* map.keys()
  ```
  let map = new Map([
    ['F', 'no'],
    ['T',  'yes'],
  ]);

  for (let key of map.keys()) {
    console.log(key); // F,T
  }
  ```
* map.values()
  ```
  for (let value of map.values()) {
    console.log(value); // no, yes
  }
  ```

====

* map.entries()
  ```
  let map = new Map([
    ['F', 'no'],
    ['T',  'yes'],
  ]);

  for (let item of map.entries()) {
    console.log(item[0], item[1]);
  }

  // 或者
  for (let [key, value] of map.entries()) {
    console.log(key, value);
  }

  // 等同于使用map.entries()
  for (let [key, value] of map) {
    console.log(key, value);
  }
  ```
====
### Map结构转为数组结构
----
* 使用扩展运算符（...）
  ```
  let map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three'],
  ]);

  [...map.keys()]
  // [1, 2, 3]

  [...map.values()]
  // ['one', 'two', 'three']

  [...map.entries()]
  // [[1,'one'], [2, 'two'], [3, 'three']]

  [...map]
  // [[1,'one'], [2, 'two'], [3, 'three']]
  ```

====
### WeakMap 数据结构
----
* 只接受对象作为键名，键名是对象的弱引用，即当对象被垃圾回收或删除后，值也自动删除 {:&.zoomIn}
* WeakMap有助于防止内存泄漏
* 只有四个方法可用：get()、set()、has()、delete() ，没有size属性，clear()方法和遍历方法

[/magic]


[slide]
[magic data-transition="move"]
## Iterator迭代器
====
### 迭代器协议
----
* Iterator迭代器就是一种协议或约定，为各种不同的数据结构提供统一的遍历机制 {:&.zoomIn}
* 任何实现了迭代器协议的数据结构都可以被for ... of 遍历，包括string,array,set,map,promise.all,扩展运算符,yield *
* 一种数据结构只要实现了Iterator协议，我们就称这种数据结构是 "可遍历的（iterable）"
* Symbol.iterator属性（是一个方法）就是实现了迭代器协议的函数
* 一个可遍历的数据结构，它必须实现一个 next() 的方法，可选方法有 return(), throw()
* Object对象没有实现迭代器协议，因此它不能用for ... of 遍历

====
### 迭代器示例
----
* 模拟具有next方法的迭代器 {:&.zoomIn}
  ```
  var it = makeIterator(['a', 'b']);

  it.next() // { value: "a", done: false }
  it.next() // { value: "b", done: false }
  it.next() // { value: undefined, done: true }

  function makeIterator(array) {
    var nextIndex = 0;
    return {
      next: function() {
        return nextIndex < array.length ?
          {value: array[nextIndex++], done: false} :
          {value: undefined, done: true};
      }
    };
  }
  ```
* next方法返回一个对象，包括value和done两个属性，value属性返回当前位置的成员，done属性表示遍历是否结束

====
### 又一个迭代器示例
----
* 无限运行的迭代器，ID生成器
  ```
  var it = idMaker();

  it.next().value // '0'
  it.next().value // '1'
  it.next().value // '2'
  // ...

  function idMaker() {
    var index = 0;

    return {
      next: function() {
        return {value: index++, done: false};
      }
    };
  }
  ```
====
### 迭代器要点总结
----
* 为所有数据结构，提供了一种统一的访问机制，即for...of循环 {:&.zoomIn}
* 只要实现了迭代器接口的数据结构都可以认为是可迭代（可遍历的）
* string, array, set, map, promise.all, 扩展运算符, yield* 默认实现了[Symbol.iterator]属性
* Object对象没有实现迭代器接口，不能用for...of遍历
* 实现迭代器接口，必须实现next()方法，可选方法有 return()方法（提前退出）, throw()方法（抛出异常）


[/magic]


[slide]
[magic data-transition="move"]
## for...of 循环
====
### 一种新的循环语句
----
* ES6 引入了for...of循环，作为遍历所有数据结构的统一的方法。 {:&.zoomIn}
* 一个数据结构只要实现了Symbol.iterator属性（方法），就可以用for...of遍历成员
* for...of循环可以代替数组实例的forEach方法
  ```
  const arr = ['red', 'green', 'blue'];

  arr.forEach(function (element, index) {
    console.log(element); // red green blue
  });

  for(let a of arr){
      console.log(a); // red green blue
  }
  ```

====
### Set和Map结构使用 for...of 循环
----
* 遍历的顺序是按照各个成员被添加进数据结构的顺序
  ```
  var es6 = new Map();
  es6.set("edition", 6);
  es6.set("committee", "TC39");
  es6.set("standard", "ECMA-262");
  for (var [name, value] of es6) {
    console.log(name + ": " + value);
  }
  // edition: 6
  // committee: TC39
  // standard: ECMA-262
  ```

====
### 类似数组的对象
----
* 类似数组的对象，如字符串，Dom nodelist, arguments，可以被遍历 {:&.zoomIn}
* 不是所有类似数组的对象都有iterator接口
  ```
  let arrayLike = { length: 2, 0: 'a', 1: 'b' };
  // 报错
  for (let x of arrayLike) {
    console.log(x);
  }
  // 正确
  for (let x of Array.from(arrayLike)) {
    console.log(x);
  }
  ```
* 一种用for...of遍历Object对象的方法
  ```
  for (var key of Object.keys(someObject)) {
    console.log(key + ': ' + someObject[key]);
  }
  ```

====
### for ... in 循环的缺点
----
* 数组的键名是数字，但是for...in循环是以字符串作为键名"0","1","2" {:&.zoomIn}
* for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键
* 某些情况下，for...in循环会以任意顺序遍历键名
* for...in循环主要是为遍历对象而设计的，不适用于遍历数组

====
### for ... of 循环的优点
----
* 有着同for...in一样的简洁语法，但是没有for...in那些缺点 {:&.zoomIn}
* 不同用于forEach方法，它可以被break、continue和return
* 提供了遍历所有数据结构的统一操作接口

[/magic]


[slide]
## 深入理解ES6特性的好处
----
* 学习框架更容易，如Koa, express, react, vue
* 很多优秀的开源项目源码都采用了ES6特性, 看源码无压力
* ES6是前端技术发展的里程碑，理解ES6可以更好地理解ES7, ES8..., 以免掉队


[slide]
## 谢谢
#### 欢迎关注 [github](https://github.com/daviscai)
----
###### 本ppt部分内容来自阮一峰老师的[《ES6标准入门》](http://es6.ruanyifeng.com/)一书
