## Node.js v8.1.2 文档
http://nodejs.cn/api/
https://v8docs.nodesource.com/

不打算写成接口使用文档，官方文档已经写的很清晰了，这里主要为了深入理解接口，包括接口解决什么问题？ 怎么使用？ 底层怎么实现？ 是否有其他方案？ 弄清楚这几个问题，对接口的理解就更加深入

### Async Hooks (异步回调)

###### 是什么？ 解决什么问题？ 应用场景是什么？
async_hooks 模块提供了一个用于`注册回调函数的 API`，这些回调函数可`追踪在 Node.js 应用中创建的异步资源的生命周期`。

请求异步资源之后一般需要通过回调来执行后续操作，比如连接服务器net.createServer，打开文件fs.open，而这些异步操作可能在回调函数执行前就被终止了，这样回调函数就永远也不会被执行，而async_hooks是对异步资源的一种管理，能够`追踪在 Node.js 应用中创建的异步资源的生命周期`，包括 init, before, after, destroy ，这样当异步操作异常退出时就会执行注册的destroy函数，从而捕获到异常信息。

这里所谓的资源是指 Node.js 底层 libuv 中的各类短期请求和长期句柄，比如定时器，连接数据库，读写文件等等。

###### 怎么用？

可以通过以下方式使用：
```
const async_hooks = require('async_hooks');

// 返回当前执行上下文的异步资源ID
const asyncId = async_hooks.currentId();

// 返回当前正在执行的回调函数的资源ID
const triggerId = async_hooks.triggerId();

// createHooks函数可以为每一个异步操作的生命周期注册钩子函数。钩子函数是可选的
const asyncHook = async_hooks.createHook({ init, before, after, destroy });

// 启用
asyncHook.enable();  
或者  
const hook = async_hooks.createHook(callbacks).enable();

// 禁用
asyncHook.disable();

function init(asyncId, type, triggerId, resource) { }

function before(asyncId) { }

function after(asyncId) { }

function destroy(asyncId) { }

```

`createHooks`函数可以为每一个异步操作的生命周期注册钩子函数，钩子函数会被每一个异步操作的不同生命周期事件调用，可以通过 currentId 来关联不同调用栈信息。

实例：
```
const async_hooks = require('async_hooks');
async_hooks.createHook({
  init(asyncId, type, triggerId) {
    const cId = async_hooks.currentId();
    fs.writeSync(
      1, `${type}(${asyncId}): trigger: ${triggerId} scope: ${cId}\n`
    );
  }
}).enable();

require('net').createServer((conn) => {}).listen(8080);
```

输出：
```
TCPWRAP(2): trigger: 1 scope: 1
TCPWRAP(4): trigger: 2 scope: 0
```

TCPWRAP表示tcp连接数

##### class AsyncResource()

钩子函数是在生命周期事件发生时自动调用的，假如我们想主动执行，类似拦截器一样，不想自动执行钩子函数，可以通过  `class AsyncResource()` 来做

```
const { AsyncResource } = require('async_hooks');

/**
 * type <string> the type of ascyc event
 * triggerId <number> the ID of the execution context that created this async event, 可选
 */
const asyncResource = new AsyncResource(type, triggerId);

// Call AsyncHooks before callbacks.
asyncResource.emitBefore();

// Call AsyncHooks after callbacks.
asyncResource.emitAfter();

// Call AsyncHooks destroy callbacks.
asyncResource.emitDestroy();

// Return the unique ID assigned to the AsyncResource instance.
asyncResource.asyncId();

// Return the trigger ID for the AsyncResource instance.
asyncResource.triggerId();

class DBQuery extends AsyncResource {
  constructor(db) {
    super('DBQuery');
    this.db = db;
  }

  getInfo(query, callback) {
    this.db.get(query, (err, data) => {
      // 回调执行之前
      this.emitBefore();

      // 执行回调
      callback(err, data);

      // 回调执行之后
      this.emitAfter();
    });
  }

  close() {
    this.db = null;
    this.emitDestroy();
  }
}
```


###### 底层怎么实现？

###### 有没有其他方案？


### Buffer (缓冲)


### Child Processes (子进程)

### Cluster (集群)

### Console (控制台)

### Crypto (加密)

### DNS (域名服务器)

### Error (错误)

### Events (事件)

### File System (文件系统)

### Global (全局变量)

### HTTP

### HTTPS


### Module (模块)

### Net (网络)

### OS (操作系统)

### Path (路径)

### Process (进程)

### Query Strings (查询字符串)

### Readline (逐行读取)

### REPL (交互式解释器)

### Stream (流)

### Timer (定时器)

### TLS (安全传输层)

### TTY (终端)

### UDP/Datagram (数据报)

### URL

### Util (实用工具)

### V8

### VM (虚拟机)

### ZLIB (压缩)
