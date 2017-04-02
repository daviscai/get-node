## 理解 Callback and Promise and Async/await

### 1. 首先了解下概念：
1. Callback : 即回调函数，是异步事件的处理程序，当触发了异步事件，如读写文件等，JS解析器（v8或者浏览器）会把事件放到一个队列里，主进程继续执行，同时，JS解析器会轮询这个事件队列，当某个事件满足运行条件时，比如文件读取完毕或者setTimeout时间已到，就会执行这个事件对应的处理程序，这个程序就是我们说的回调函数。  
`简单地讲，这样定义了一个匿名的回调函数：setTimeout(function(){},1000);`

2. Promise： Promise 对象是一个代理对象（代理一个值），被代理的值在Promise对象创建时可能是未知的。它允许你为异步代码执行结果的成功和失败分别绑定相应的处理方法（handlers ）。 这让异步方法可以像同步方法那样返回值，但是并非立即返回执行的结果，因为毕竟执行的是异步代码，因此，它会返回一个Promise对象，如前所说，它是一个代理的对象，代理了最终返回的值，可以在后期使用。      
`简单理解就是，我们把异步处理代理给Promise对象，Promise对象会保证返回结果给我们(通过.then()方法或者.catch()方法返回结果)，我们可以用类似同步的思维来处理异步逻辑，但还不是完全同步的方式`

3. Async/await ： ES2017新增的处理异步逻辑的一种方式，也是推荐使用的一种方式，或者说是 Generator 函数的语法糖，内部实现原理比较简单，自带执行器，在执行器里通过Promise对象代理异步操作，返回一个Promise对象。更多查看 [async 函数的含义和用法](http://www.ruanyifeng.com/blog/2015/05/async.html)  
`Async/await 是 Generator函数 + co + Promise，这样就做到了完全同步方式处理异步逻辑`

### 2. 解决实际问题，根据实际项目中遇到的问题，抽象应用场景

应用场景是：上传文件

我们假设上传是异步的，通过回调函数返回上传后的文件信息和表单字段，这种方式也是一些上传组件的处理方式，比如：[formidable](https://github.com/felixge/node-formidable)，我们先用setTimeout来模拟异步上传。

```
function main(){
   let up = new Upload()
   up.send(function(err, field, file){
      // upload finished
	   console.log(field, file);
   });

   let rs = {url:'http://2222'}
   console.log(rs);
}

// handler upload stream
class Upload{

  send (cb){
      setTimeout(function(){
        let field = 'aaa';
        let file = 'bbbb';
        cb(null, field, file);
      },100);
  }
}

main();
```

输出：
```
Object {url: "http://2222"}
aaa bbbb
```

执行mian方法，先打印 rs 变量，然后再打印upload方法返回值，跟预期一样，但这是有问题的，因为实际需求通常需要在上传后，先获取上传文件信息，再做业务逻辑处理，看看下面代码：

```
function main(){
   let up = new Upload()
   up.send('hello', function(err, field, file){
      // upload finished
      console.log(field);
   });

   console.log('something else');
   return '';
}

// handler upload stream
class Upload{

  send (t, cb){
      setTimeout(function(){
        let field = t;
        let file = 'bbbb';
        cb(null, field, file);
      },100);
  }
}

var a = main();
```

输出：
```
something else
hello
```

我们会发现在main()方法里无法获得异步事件的结果，这就是callback回调函数带来的问题，无法满足业务需求。

我们再来梳理下业务需求：
1. 上传文件是异步的
2. 主函数必须等上传完成后，获取到文件信息才能继续往下走
3. 主函数是一个有返回值的函数

我们可以用promise 结合 async/await 来做到`用同步代码实现异步逻辑`，同时满足业务需求。

```
async function main(){

   let up = new Upload()   
   let parse = promisify(up.send, up); // promisify是把callback函数转换成Promise对象
   let rs = await parse('hello');   
   console.log(rs)
   console.log('something else');
   return rs;
}

// handler upload stream
class Upload{

  send (t, cb){
      setTimeout(function(){
        let field = t ;
        let file = 'file info';
        cb(null, field, file);
      },100);
  }

}

//把callback函数转换成Promise对象
function promisify(fn, receiver){
  return (...args) => {
    return new Promise((resolve, reject) => {
      fn.apply(receiver, [...args, (err, res) => {
        return err ? reject(err) : resolve(res);
      }]);
    });
  };
}

var a = main();
```

输出：
```
hello
something else
```

输出如预期，通过promise + async/await 实现了业务需求，具体思路如下：
1. 把callback回调封装成promise
2. 用 async/await 方式处理 promise 异步请求

但是promisify方法有一个问题，就是resolve函数只支持一个参数，如果callback函数有多个参数，只能接收到第一个参数，上面例子中，Upload.send方法里回调函数回传了3个参数：cb(null, field, file); 假设这是第三方库，我们无法修改它，有没有办法解决呢？

其实bluebird的promisify方法已支持回传多个参数的情况：

```
const Promise = require('bluebird')

async function main(){

   let up = new Upload()   
   //let parse = promisify(up.send, up);

   // Setting multiArgs to true means the resulting promise will always fulfill with an array of the callback's success value(s)
   var parse = Promise.promisify(up.send, {context: up, multiArgs:true});
   let rs = await parse('hello');   
   console.log(rs)
   console.log('something else');
   return rs;
}

// handler upload stream
class Upload{

  send (t, cb){
      setTimeout(function(){
        let field = t ;
        let file = 'file info';
        cb(null, field, file);
      },100);
  }

}

var a = main();
```

这样就解决了所有问题，实现了业务需求，其实这个业务场景是我在做图片压缩服务时候遇到的坑，类似tinypng.com在线服务，可以部署到本地，已开源 [tinyimg](https://github.com/daviscai/tinyimg)，欢迎提issue和star


### 引申问题

1. Callback 和 Promise有什么区别？  
    Callback回调函数是异步事件的处理程序，是语言自带的处理机制，但这样容易出现回调地狱问题；  
    Promise是对回调函数的代理，并且保证返回结果(成功或者失败)，可以用同步的方式处理异步操作

    Javascript 中最常见的回调地狱
```
    // 回调金字塔
    asyncOperation(function(data){
      // 处理 `data`
      anotherAsync(function(data2){
          // 处理 `data2`
          yetAnotherAsync(function(){
              // 完成
          });
      });
    });
```

    引入 Promises 之后的代码
```
    promiseSomething()
    .then(function(data){
        // 处理 `data`
        return anotherAsync();
    })
    .then(function(data2){
        // 处理 `data2`
        return yetAnotherAsync();
    })
    .then(function(){
        // 完成
    });
```

    看看Promise实际例子：
```
    var myFirstPromise = new Promise(function(resolve, reject){
        //当异步代码执行成功时，我们才会调用resolve(...), 当异步代码失败时就会调用reject(...)
        //在本例中，我们使用setTimeout(...)来模拟异步代码。
        setTimeout(function(){
            resolve("成功!"); //代码正常执行！
        }, 250);
    });

    myFirstPromise.then(function(successMessage){
        //successMessage的值是上面调用resolve(...)方法传入的值.
        //successMessage参数不一定非要是字符串类型，这里只是举个例子
        console.log("Yay! " + successMessage);
    });
```

    输出：
```
    Yay! 成功!
```


### 延伸阅读
https://75team.com/post/how-to-convert-callback-to-promise.html
https://blog.coding.net/blog/how-do-promises-work
http://imweb.io/topic/565af932bb6a753a136242b0  
https://oshotokill.gitbooks.io/understandinges6-simplified-chinese/content/chapter_11.html
