# 《深入浅出node.js》
## 第1章 Node简介
单线程的弱点：
1. 无法利用多核CPU。
2. 错误会引起整个应用退出，应用的健壮性值得考验。
3. 大量计算占用CPU导致无法继续调用异步I/O。

## 第2章 模块机制
如果想对自定义的扩展名进行特殊的加载，可以通过类似require.extensions['.ext']的方式实现
```javascript
// Native extension for .json
Module._extensions['.json'] = function(module, filename) {
    var content = NativeModule.require('fs').readFileSync(filename, 'utf8');
    try {
        module.exports = JSON.parse(stripBOM(content));
    } catch (err) {
        err.message = filename + ': ' + err.message;
        throw err;
    }
};
```

在此之外还介绍了：
* 不同类型模块的加载方式
* npm用法
* cmd、amd

## 第3章 异步I/O

* 介绍了node中的异步I/O的思想、实现原理（实现原理看不懂）。
* setTimeout、process.nextTick等api的执行先后（这个推荐看《Node.js调试指南》的第3章）



## 第4章 异步编程
由于这本书比较旧了，所以这章的内容也比较落后了，姑且看看

* 描述的异步编程的优缺点
* 接下来的一些异步编程相关的库
* 异步并发控制：
    1. 任务队列
    2. 拒绝模式
    3. 超时控制

## 第5章 内存控制
* 这部分说了一些虚拟机GC相关的算法(比较详细)
* 内存状态的api，应该可以用来做监控系统
* 内存泄漏：
    * 产生原因：
        1. 缓存
        2. 队列消费不及时
        3. 作用域未释放
    * 排查方法：
        1. v8-profiler
        2. node-heapdump
        3. node-mtrace
        4. dtrace
        5. node-memwatch

## 第6章 理解Buffer
* 有一个buffer拼接的相关例子，用于避免编码带来的问题（值得参考）
* highWaterMark和性能的关系



## 第7章 网络编程
* 大部分内容是那些api介绍
* websocket握手的过程值得一看
* tls/ssl的原理值得一看

## 第8章 构建Web应用
只记录值得参考的部分：
* 把登录口令放在queryString的方法：没有口令就生成一个，然后重定向到带口令的对应地址。
* cookie加密的原理
    1. 攻击者会猜测cookie的生成方法，来碰撞已有用户的cookie<br>
    解决办法：在原有的cookie后面，加入以cookie、sercet、私钥加密成的hash，由于攻击者不知道后两者，所以无法生成猜测的cookie
    2. 攻击者获取了cookie，<br>
    解决办法：在用户进行访问的时候，set-cookie时，把用户对应的ua、ip等信息加密进cookie里面，由于攻击者的这些条件可能对应不上，就算获取到了cookie也没有用

* 数据上传与安全：
    1. 内存限制（限制报文大小）

* 渲染：
    1. Bigpipe技术：
        * 先把框架模板发送到前端，之后的内容用\<script>来填充
        * 一整个页面都在一个http请求里面完成


## 第9章 玩转进程
* Master-Worker模式，又称主从模式
```javascript
//worker.js
var http = require('http');

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World\n');
}).listen(Math.round((1 + Math.random()) * 1000), '127.0.0.1');
```
```javascript
// master.js
var fork = require('child_process').fork;
var cpus = require('os').cpus();
for (var i = 0; i < cpus.length; i++) {
    fork('./worker.js');
}
```

* 4种方法的差别

|类型|回调/异常|进程类型|执行类型|可设置超时|
|---|---|---|---|---|
|spawn|×|任意|命令|×|
|exec|√|任意|命令|√|
|execFile|√|任意|可执行文件|√|
|fork|×|Node|js文件|×|


* 进程间通信
```javascript
// parent.js
var cp = require('child_process');
var n = cp.fork(__dirname + '/sub.js');
n.on('message', function (m) {
    console.log('PARENT got message:', m);
});
n.send({hello: 'world'});
// sub.js
process.on('message', function (m) {
    console.log('CHILD got message:', m);
});
process.send({foo: 'bar'});
```

* 句柄传递<br>
句柄：是一种可以用来标识资源的引用，它的内部包含了指向对象的文件描述符。<br>
比如句柄可以用来标识一个服务器端socket对象、一个客户端socket对象、一个UDP套接字、一个管道等<br>
由于进程每接收到一个连接，将会用掉一个文件描述符，因此代理方案中客户端连接到代理进程，代理进程连接到工作进程的过程需要用掉两个文件描述符。<br>
操作系统的文件描述符是有限的，代理方案浪费掉一倍数量的文件描述符。
    1. 句柄发送与还原<br>
        发送到IPC管道中的实际上是我们要发送的句柄文件描述符，文件描述符实际上是一个整数值(也就是说send方法不能发送任意对象)
    2. 端口共同监听<br>
        由于独立启动的进程互相之间并不知道文件描述符，所以监听相同端口时就会失败。<br>
        但对于send()发送的句柄还原出来的服务而言，它们的文件描述符是相同的，<br>
        所以监听相同端口不会引起异常。<br>
        多个应用监听相同端口时，文件描述符同一时间只能被某个进程所用。<br>
        换言之就是网络请求向服务器端发送时，只有一个幸运的进程能够抢到连接，<br>
        也就是说只有它能为这个请求进行服务。<br>
        这些进程服务是抢占式的。

```javascript
// parent.js
var cp = require('child_process');

var child1 = cp.fork('child.js');
var child2 = cp.fork('child.js');

var server = require('net').createServer();

server.listen(1337, function () {
    child1.send('server', server);
    child2.send('server', server);
    // 关掉
    server.close();
});

// child.js
var http = require('http');

var server = http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('handled by child, pid is ' + process.pid + '\n');
});

process.on('message', function (m, tcp) {
    if (m === 'server') {
        tcp.on('connection', function (socket) {
            server.emit('connection', socket);
        });
    }
});
```
* 集群稳定之路
    1. 进程事件
    ```javascript
    //除了send以外
    //进程间还可以通过kill函数发送事件

    // 子进程
    child.kill([signal]);
    // 当前进程
    process.kill(pid, [signal]);
    ```
    2. 自动重启<br>
    我们能够通过监听子进程的exit事件来获知其退出的信息，<br>
    接着前文的多进程架构，我们在主进程上要加入一些子进程管理的机制，<br>
    比如重新启动一个工作进程来继续服务。

    3. 负载均衡

    ```javascript
    // 启用Round-Robin
    cluster.schedulingPolicy = cluster.SCHED_RR
    // 不启用Round-Robin
    cluster.schedulingPolicy = cluster.SCHED_NONE
    ```
    
    4. 状态共享（Node也不允许在多个进程之间共享数据）
        * 第三方数据存储（数据库、磁盘文件、缓存服务（如Redis））
        * 主动推送（用TCP\UDP推送更改）

* cluster模块(与前文创建子进程集群的效果相)
```javascript
// cluster.js
var cluster = require('cluster');
cluster.setupMaster({
    exec: "worker.js"
});

var cpus = require('os').cpus();

for (var i = 0; i < cpus.length; i++) {
    cluster.fork();
}
```

## 第10章 测试
* 单元测试
* 性能测试
    1. 基准测试
    2. 压力测试：对网络接口进行压力测试以判断网络接口的性能<br>
    (貌似是只需要一行命令行就行)
    3. 基准测试驱动开发
    4. 测试数据与业务数据的转换（qps = pv / time）<br>
    qps: 每秒访问数<br>
    pv:  每天的用户量<br>
    time: 访问量集中时间<br>

## 第11章 产品化
* 项目工程化
    1. 目录结构
    2. 构建工具
    3. 编码规范
    4. 代码审查（git拉分支，在有合并请求的时候对代码进行评审）
* 部署流程
    1. 部署环境（说得很简略）
    2. 部署操作（pm2）
* 性能
    1. 动静分离（nginx、cdn）
    2. 启用缓存（redis）
    3. 多进程架构（第9章）
    4. 读写分离（数据库相关：写数据库的锁会影响读）
* 日志
    1. 访问日志（运营数据用于帮助改进、也可以分析爬虫定点禁止）
    2. 异常日志
    3. 日志与数据库（日志存放地点的优缺。文件：快、没有sql；数据库：慢、有sql）
    4. 分割日志
* 监控报警(一种是业务逻辑型的监控，一种是硬件型的监控)
    1. 监控
        * 日志监控（异常监控、qps监控）
        * 响应时间（这里提及的是服务器请求一进一出的响应时间；现在连浏览器也有响应的性能api可以用作监控了）
        * 进程监控（在多进程架构下要检查操作系统中的工作进程数）
        * 磁盘监控（主要是日志在消耗磁盘空间）
        * 内存监控（用于监控内存泄漏问题）
        * CPU占用监控（用户态小于70%、内核态小于35%且整体小于70%时，处于健康状态）
        * CPU load监控（CPU在单位时间内正在使用和等待使用CPU的平均任务数）
        * I/O负载（磁盘读写）
        * 网络监控（流入流量和流出流量）
        * 应用状态监控（数据库连接是否正常、缓存是否正常等）
        * DNS监控
    2. 报警的实现
        * 邮件报警
        * 短信或电话报警
* 稳定性
    1. 多机器（负载均衡、状态共享和数据一致性）
    2. 多机房（解决地理位置延迟、互为备份）
    3. 容灾备份
* 异构共存（不同技术间的共存）（方法是：采用标准协议）

