# 《Node.js调试指南》
## 第1章 CPU
```
介绍了火焰图、差分火焰图，用他们来进行性能分析
和一些生成相关的命令，这个实在是太难记了，而且linux限定。
```
火焰图的读法还是看这个比较易懂：[如何读懂火焰图？ - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2017/09/flame-graph.html)

差分火焰图用于修改前后的对比：执行的函数的次数增多则标红，减少则标蓝

```
本人更推荐用node的--inspect或--inspect-brk参数，
配合chrome的chrome://inspect/#devices，
来生成.cpuprofile文件
可以用npm上的flamegraph来生成火焰图
```
```
其余也介绍了v8-analytics这个工具，至于具体用法还得自己去找
```
```
node --prof xxx
可以生成性能日志

node --prof-process xxx.log
用于分析性能日志

里面有简单的阅读方法，但是像蜻蜓点水一样没有多少内容。
```





## 第3章 代码
1. 介绍了Promise的实现，和使用demo，对于我来说没有多大用处。
2. 使用async/await的原因：调用栈更加清晰（这个也是废话）
3. 优化异常栈信息(v8独家)<br>
Error.captureStackTrace
```javascript
const myObject = {};
Error.captureStackTrace(myObject);

//会输出captureStackTrace调用时的栈帧信息
console.log(myObject.stack);
```
Error.prepareStackTrace
```javascript
Error.prepareStackTrace = function(error, callSites) {
    error.toString();

    //可以获取调用相关的各种元数据
    //元数据，骚操作的味道
    callSites.getFunctionName();
    callSites.getLineNumber();
    callSites.getFileName();
    callSites.getColumnNumber();
    callSites.getThis();
    callSites.getTypeName();
    callSites.getFunction();
    callSites.getMethodName();
    callSites.getEvalOrigin();
    callSites.isToplevel;
    callSites.isEval;
    callSites.isName;
    callSites.isConstructor;

    return "console.error输出的信息"
}
```
Error.stackTraceLimit(改变输出的行数)
4. 可以使用Rust开发node模块
```javascript
//rust模块的调用

const ffi = require(ffi);
const rust = ffi.Library("rust生成的动态链接库路径", {
    // 方法名：[输入列表, [输出]]
    "fib": ["int", ["int"]]
})

//方法调用
rust.fib(1)

//后面还介绍了rust编写模块的方法，使用的时Neon的手脚架，
//具体的编码逻辑等还要看《Node.js来一打C++扩展》
```
5. node的任务队列：(这里有大量的事例用来说明这些api的执行顺序，推荐复习)
    1. timers：执行setTimerout()和setInterval()中到期的callback
    2. I/O callbacks：上一轮循环中有少数的I/O callback会被延迟到这一轮执行
    3. idle，prepare：仅内部使用
    4. poll：最重要的阶段，执行I/O callback，在适当的条件下node会阻塞这个阶段
    5. check：执行setImmediate()的callback
    6. close callbacks：执行close事件的callback，如：socket.on("close", cb);
    * poll阶段：
        1. 当timers的定时器到期后，执行定时器的callback
        2. 执行poll队列里面的I/O callback
        * 如果poll queue不为空，则event loop将同步执行queue里的callback，直至queue为空，或者执行的callback达到系统上限。
        * 如果poll queue为空，则可能发生一下情况。
            * 如果代码使用setImmediate()设定了callback，则event loop将结束pool阶段并进入check阶段，执行check阶段的queue
            * 如果代码没有使用setImmediate()，则event loop将阻塞在该阶段，等待callbacks加入poll queue，如果有callback进来则立即执行
    * process.nextTick()不在event loop的任何阶段，而在各个阶段切换的中间执行。

6. process.on("uncaughtException", cb);


## 第4章 工具

* source-map-support修改了Error.prepareStackTrace，使压缩后的代码能配合source-map打印出压缩前的异常栈。
* 调试正在运行的node程序
    1. 找出该程序pid
    2. ```node -e "process._debugProcess(pid)"```
    3. chrome://inspect/#devices
* vscode调试器配置
* Hot Reload的实现原理（这个东西具体还是挺复杂的，建议复习）
1. 使用Proxy将模块导出对象包装好
2. 在获取对象的属性时，获取最新的require.cache对象上的属性
3. 监听代码文件，如果有修改，则更新require.cache
