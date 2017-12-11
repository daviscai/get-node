## 理解设计模式 - 策略模式

### 什么是策略模式
策略模式的定义是:定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

比如一个压缩文件的程序，既可以选择 zip 算法，也可以选择 gzip 算法。这些算法灵活多样，而且可以随意互相替换。

从定义上看，策略模式就是用来封装算法的。其实，这里的算法可以理解成广义的算法，即业务逻辑的实现也可以认为是算法。

### Javascript实现

举个例子：表单校验

通常我们对于简单的表单验证都是这么处理的（伪代码）：
```
var validator = {
    validate: function (value, type) {
        switch (type) {
            case 'isNonEmpty ':
                return true; // NonEmpty 验证结果
            case 'minLength ':
                return true; // minLength 验证结果
                break;
            case 'isMobile ':
                return true; // isMobile 验证结果
            default:
                return true;
        }
    }
};
```
那么如果我们要新加验证规则，就需要修改代码了，validator极有可能会变成很大的函数，后期维护成本极大。

我们可以用策略模式来重构下，把不同的验证规则（算法）封装起来，通过一个策略类来统一处理，使用时候可以灵活选择采用哪种验证规则(算法)。

策略类：
```
var validator = {

    // 所有可以的验证规则处理类存放的地方，后面会单独定义
    types: {},

    // 验证类型所对应的错误消息
    messages: [],

    // 暴露的公开验证方法
    // 传入的参数是 key => value对
    validate: function (data) {

        // 清空所有的错误信息
        this.messages = [];

        for (var i in data) {
            if (data.hasOwnProperty(i)) {

                // 遍历需要验证的规则
                for( var j in data[i]["rule"] ){
                    type = data[i]["rule"][j];  // 根据key查询是否有存在的验证规则
                    checker = this.types[type]; // 获取验证规则的验证类

                    result_ok = checker.validate(data[i]["value"]); // 使用查到到的单个验证类进行验证
                    if (!result_ok) {
                        msg = "验证失败提示"
                        this.messages.push(msg);
                    }
                }
            }
        }
        return this.hasErrors();
    },

    // helper
    hasErrors: function () {
        return this.messages.length !== 0;
    }
};

```

单独封装的各个策略算法：

```
// 验证给定的值是否不为空
validator.types.isNonEmpty = {
    validate: function (value) {
        return value !== "";
    }
};

// 验证给定的值是否是数字
validator.types.isNumber = {
    validate: function (value) {
        return !isNaN(value);
    }
};

// 验证给定的值是否只是字母或数字
validator.types.isAlphaNum = {
    validate: function (value) {
        return !/[^a-z0-9]/i.test(value);
    }
};
```

表达验证：

```
var data = {
    "first_name": {"value":"Tom","rule":["isNonEmpty","isNumber"]}
}
validator.validate(data);
if (validator.hasErrors()) {
    console.log(validator.messages.join("\n"));
}
```

通过策略模式，把多个算法封装起来，非常方便扩展，以后新加验证规则，只需要新加一个验证算法就可以了，主函数代码无需修改，满足开放-封闭原则。


策略模式优点：
1. 策略模式可以有效避免多重条件选择语句。
2. 策略模式提供了对开放-封装原则的完美支持，将方法封装在独立的strategy中，使得它们易于切换，易于理解，易于扩展。
3. 复用性高。

策略模式缺点：
1. 增加了许多策略类或者策略对象。
2. 要使用策略模式，必须了解所有的strategy，违反了最少知识原则。

适用场景：
1. 如果在一个系统里面有许多类，它们之间的区别仅在于它们的行为，那么使用策略模式可以动态地让一个对象在许多行为中选择一种行为。
2. 一个系统需要动态地在几种算法中选择一种。
3. 一个类定义了多种行为, 并且这些行为在这个类的操作中以多个条件语句的形式出现。将相关的条件分支移入它们各自的Strategy类中以代替这些条件语句。
