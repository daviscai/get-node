## 理解栈

#### 栈的概念
栈是遵循`后进先出`（LIFO）的`有序`集合。 就像羽毛球的球筒，先放的球在下面，后放的球在上面。栈通常被用于在内存中保存变量和方法调用。

#### 用Javascript实现一个栈
栈通常有下面几个操作：
1. push(element) : 添加新元素到栈顶
2. pop() : 出栈，移除栈顶的元素，同时返回该元素
3. peek(): 返回栈顶元素
4. isEmpty():判断栈是否为空
5. clear(): 清空栈
6. size(): 返回栈里的元素

我们用数组来实现栈

```
function Stack(){
    var items = [];
}
```

实现第一个方法 push(element)

```
function Stack(){
    var items = [];

    //入栈
    this.push(element){
        items.push(element);
    }
}
```
利用数组的push方法非常简单就实现了，下面实现pop()方法

```
function Stack(){
    var items = [];

    //入栈
    this.push(element){
        items.push(element);
    }

    //出栈
    this.pop(){
        return items.pop();
    }
}
```

查询栈顶元素：
```
function Stack(){
    var items = [];

    //入栈
    this.push(element){
        items.push(element);
    }

    //出栈
    this.pop(){
        return items.pop();
    }

    // 查询栈顶元素
    this.peek(){
        return items[items.length -1];
    }
}
```

判断栈是否为空
```
function Stack(){
    var items = [];

    //入栈
    this.push(element){
        items.push(element);
    }

    //出栈
    this.pop(){
        return items.pop();
    }

    // 查询栈顶元素
    this.peek(){
        return items[items.length -1];
    }

    // 判断栈是否为空
    this.isEmpty(){
        return items.length == 0;
    }
}
```

下面是清空栈元素和返回栈元素个数
```
function Stack(){
    var items = [];

    //入栈
    this.push = function(element){
        items.push(element);
    }

    //出栈
    this.pop = function(){
        return items.pop();
    }

    // 查询栈顶元素
    this.peek = function(){
        return items[items.length -1];
    }

    // 判断栈是否为空
    this.isEmpty = function(){
        return items.length == 0;
    }

    //清空
    this.clear = function(){
        items = [];
    }

    //返回栈元素个数
    this.size = function(){
        return items.length;
    }

    this.echo = function(){
        console.log(items.toString());
    }
}
```


#### 使用栈

我们现在实现了栈类，下面来测试下

```
var stack = new Stack();
console.log(stack.isEmpty()); //判断是否为空
stack.push(100)
stack.push('abc')
stack.echo();

console.log(stack.peek());
console.log(stack.pop());
console.log(stack.size());

stack.clear();
console.log(stack.size());

```
输出：
```
true
100,abc
abc
abc
1
0
```

下面来看看实际应用场景：

#### 数制间的相互转换：

可以利用栈将一个数字从一种数制转换成另一种数制。假设想将数字 n 转换为以 b 为基数的数字,实现转换的算法如下。

1. 最高位为 n % b,将此位压入栈。  
2. 使用n/b代替n。  
3. 重复步骤 1 和 2,直到 n 等于 0,且没有余数。  
4. 持续将栈内元素弹出,直到栈为空,依次将这些元素排列,就得到转换后数字的字符串形式。  

```
function mulBase(num, base) {
    var s = new Stack();
    do {
        s.push(num % base);
        num = Math.floor(num /= base);
    } while (num > 0);
    var converted = "";
    while (s.size() > 0) {
        converted += s.pop();
    }
    return converted;
}

mulBase(115, 8); // 163
```

下面是题外话，上面算法就是采用除8取余法，余数依次入栈，最后，依次出栈就是所要的结果了。转换其他进制也是同理。
```
十进制化八进制
方法1：采用除8取余法。
例：将十进制数115转化为八进制数
8| 115…… 3
8| 14 …… 6
8| 1 …… 1
结果：（115）10 = （163）8
```

#### 汉诺塔

>有三根杆子A，B，C。A杆上有N个(N>1)穿孔圆盘，盘的尺寸由下到上依次变小。要求按下列规则将所有圆盘移至C杆：
每次只能移动一个圆盘；
大盘不能叠在小盘上面。

可以看看汉诺塔的演示： http://jsfiddle.net/timwzw/S7mYF/embedded/result/

汉诺塔的递归法：
```
function hanoi( n, a, b, c )
{
    if( n > 0 )
    {
        hanoi(n-1,a,c,b);
        //将a柱子的最上面的盘子移到c柱子
        c.push( a.pop() );
        hanoi(n-1,b,a,c);
    }
}

var a = new Stack();
a.push('h');
a.push('k');
a.push('m');

var b = new Stack();
var c = new Stack();
hanoi( 3, a, b, c )
c.echo() // h,k,m
```

显然，递归的基础是函数调用，而函数调用的基础是栈。所以每次递归的开销，则是需要不停的进行栈的push和pop操作，这样会耗费大量的空间，也可能会造成冗余运算。


## 理解队列




## 理解链表

## 理解哈希散列

## 理解集合
