## 理解正则表达式
正则表达式是用于匹配字符串中字符组合的模式。在 JavaScript中，正则表达式也是对象。

### 创建方式

在JavaScript中，我们可以通过RegExp()构造函数或者RegExp直接字面量两种方式去创建正则表达式。
```  
const regex = /ab+c/;
const regex = /^[a-zA-Z]+[0-9]*\W?_$/gi;

let regex = new RegExp("ab+c");
let regex = new RegExp(/^[a-zA-Z]+[0-9]*\W?_$, "gi");
```

直接字面量不支持变量，但构造函数的方式可以，有些时候我们需要的匹配模式不是固定的，预先并不能确定，比如
```
const a = 'ab+c';
const p = new RegExp(a);
p.test('aa'); // false
```

### 使用正则表达式的方法
| 方法 | 说明 |
| :--- | :---- |
| exec | 一个在字符串中执行查找匹配的RegExp方法，它返回一个数组（未匹配到则返回null）。|
| test	| 一个在字符串中测试是否匹配的RegExp方法，它返回true或false。|
| match	| 一个在字符串中执行查找匹配的String方法，它返回一个数组或者在未匹配到时返回null。|
| search	| 一个在字符串中测试匹配的String方法，它返回匹配到的位置索引，或者在失败时返回-1。|
| replace	| 一个在字符串中执行查找匹配的String方法，并且使用替换字符串替换掉匹配到的子字符串。|
| split	| 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的String方法。|


### 捕获匹配的片段

有些时候匹配的字符要在执行时候才能确定，比如匹配XML标签`<strong>whatever</strong>` ，特点是闭合标签跟开始标签同名，开始标签可以通过正则匹配到，但闭合标签怎么确定呢？闭合标签要依赖匹配到的开始标签，这里有个非常有用的方法，反向引用。
```
/<(\w+)>(.+)<\/\1)/
```
上面这个模式就可以满足匹配XML的需求，其中括号里的`(\w+)`是匹配到的片段，`\1`是第一个匹配到的片段，该正则模式有2个括号，也就有2个匹配片段，`\1`等于`(\w+)`，`\2`等于`(.+)`。



### 例子

1. trim去除空白字符
```
function trim(str){
    return ( str || "").replace(/^\s+|\s+$/g, "");
}
```

2. 匹配URL
```
var patternURL = /https?:\/\/[a-z0-9_.:]+\/[-a-z0-9_:@&?=+,.!/~*%$]*(\.(html|htm|shtml))?/
patternURL.test('http://hostname/path.html')
```



### 参考
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions  
http://imweb.io/topic/56e804ef1a5f05dc50643106
http://javascript.ruanyifeng.com/stdlib/regexp.html#toc18
