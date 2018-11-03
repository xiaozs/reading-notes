# 《Node.js 硬实战 115个核心技巧》
## 第1章 入门
介绍了node的一些特性，<br>
还有一个stream的使用例子（有点复杂看不太懂）

## 第2章 全局变量：Node环境
* 技巧1：安装与加载模块
* 技巧2：创建与管理模块
```javascript
//返回文件绝对路径
require.resolve(id)
```
```javascript
//卸载模块
delete require.cache(require.resolve("./myClass"))
```
* 技巧3：加载一组相关的模块<br>
（这个技巧有点意思，这个和《Dart编程语言》中第3章 导出 里面提出的问题有点关联）

```javascript
//文件后缀数组，node会依照这个数组里面的后缀解析依赖
//但这个已经被标为废弃
require.extensions
```

* 技巧4：使用路径
```javascript
__dirname       //表示当前文件所在的目录的绝对路径
__filename      //表示当前文件的绝对路径
```

* 技巧5：标准I/O流的读写
```javascript
//输入输出流
process.stdin
process.stdout
process.stderr

process.stdin.resume();                 //通知stream准备开始读取数据 
process.stdin.setEncoding("utf8");
process.tsdin.on("data", (text) => {
    process.stdout.write(text.toUpperCase());
})
```

* 技巧6：打印日志消息
```javascript
console.log     // process.stdout   console.info
console.error   // process.stderr   console.error
console.trace
```

* 技巧7：基准测试
```javascript
console.time();
console.timeEnd();
```

* 技巧8：获取平台信息
```javascript
process.arch                // "x64" "ia32" ...
process.platform
process.memoryUsage()
// => 
// {
//     rss,         //常驻内存大小
//     heapTotal,   //动态分配的可用内存
//     heapUsed     //已经使用的堆大小
// }
```

* 技巧9：传递命令行参数
```javascript
process.argv
```

* 技巧10：退出程序
```javascript
process.exit(0)     //返回0表示正常退出，其他为异常
//这个码可以在cli里面引用
```

* 技巧11：响应信号量
```javascript
process.stdin.resume();     //用于防止程序直接退出

//用于监听其他进程发出的信号
process.on("SIGHUP", () => {

})
process.pid
//给pid的进程发送信号
process.kill(pid, [signal])
```

* 技巧12：通过setTimeout延迟执行函数
* 技巧13：通过定时器定时调用函数
```javascript
var id = setInterval(cb, 1000);
id.unref();     //这个和 cleanInterval一个意思
```

* 技巧14：安全的操作异步接口
```javascript
//这个api的用途大概就是用于将那些同步/异步混用的函数转化为纯异步用的
process.nextTick(cb)

//这个会限制nextTick的递归调用次数（现已废弃）
process.maxTickDepth 
```


## 第3章 Buffers：使用比特、字节以及编码
* 技巧15：Buffer转换为其他格式
```javascript
buf.toString();
buf.toString("ascii");
```
* 技巧16：使用Buffer来修改字符串编码(同上)
* 技巧17：使用Buffer来转换原始数据<br>
（这里有一个很好懂的二进制文件分析例子，推荐复习）
* 技巧18：创建自己的网络协议（和上面那个差不对意思）


## 第4章 Events：玩转EventEmitter
* 技巧19：从EventEmitter继承
* 技巧20：混合EventEmitter
* 技巧21：管理异常
```javascript
var EventEmitter = require("events").EventEmitter;
var emitter = new EventEmitter();
//error事件做了特殊处理，在不设置的时候有默认值
//没有绑定的时候会打印异常栈
//不过还是要用来emit来触发（我觉得很傻）
emitter.on("error", () => {})
```
* 技巧22：通过domains管理异常
```javascript
//这个东西能够截获异步函数的异常
//应该是和node底层相关
//已被废弃

var domain1 = domain.create();

domain1.on('error', function(err){
    //捕获异常后执行的回调
});

domain1.run(function(){
    //这里发生的所有同/异步异常都会被捕获
});
```

* 技巧23：反射
```javascript
let EventEmitter = require("events").EventEmitter;

let emitter = new EventEmitter;

//EventEmitter自带事件，
//会在绑定新监听器的时候被触发
emitter.on("newListener", () => {
    
})
```

* 技巧24：探索EventEmitter
* 技巧25：组织事件名称
* 技巧26：EventEmitter的替代方案


## 第5章 流：最强大和最容易误解的功能

每个类的模板方法：

|Name|User Methods|Description|
|---|---|---|
|stream.Readable|_read(size)|用于在I/O上获取数据|
|stream.Writable|_write(chunk, encoding, callback)|用于在输出的目标写入数据|
|stream.Duplex|_read(size)<br>_write(chunk, encoding, callback)|一个可读和可写的流，例如网络连接|
|stream.Transform|_flush(size)<br>_transform(chunk, encoding, callback)|一个会以某种方式修改数据的双工流，没有输入数据要匹配输出数据的限制|


* 技巧27：使用内置的流来实现静态web服务器
* 技巧28：流的错误处理
```javascript
var stream = fs.createReadStream("xxx");
stream.on("error", () => {})
```
* 技巧29：使用流的第三方模块
```javascript
var stream = require("stream");
class StatStream extends stream.Readable {
    constructor(limit){
        super();
        this.limit = limit;
    }

    //模板方法
    //流被读的时候会被调用
    _read(){
        if(this.limit === 0) {
            //done
            this.push();
        } else {
            this.push(this.limit--);
        }
    }
}
```
* 技巧30：正确地从流的基类继承

|Problem|Solution|
|---|---|
|你想要使用流API来包装一个底层的I/O数据源|Readable|
|你想要从一个程序中获取输出到其他地方使用，或者在程序中发送数据|Writable|
|你想要以某种方式解析数据并且修改它|Transform|
|你想要包装一个数据源，并且它也可以接受消息|Duplex|
|你想要从流中提取数据，从测试到分析都不修改它|PassThrough|
```javascript
//继承时可以选择的选项
super({
    highWaterMark,      //停止读取底层数据源之前的内部缓冲数据的大小
    encoding,           //触发缓冲数据自动编码
    objectMode          //允许流是一个流对象，而不是字节
})
```
* 技巧31：实现一个可读流
* 技巧32：实现一个可写流
* 技巧33：使用双工流转换和接收数据
* 技巧34：使用转换流转换解析数据


结合这几个例子和node的一些源码，可以知道
1. 





* 技巧35：流的优化<br>
内置的流，允许配置内部缓冲区的大小。
```javascript
fs.createWriteStream("filePath", { bufferSize });
```
* 技巧36：使用老的流API
旧的流API是一个不断发送data事件的对象：<br>
可用```new stream.Readable().wrap(oldStream)```来生成一个新API对象

* 技巧37：基于功能的流适配
```process.stdin.isTTY```<br>
```process.stdout.isTTY```<br>
用于判断是否运行在TTY（用户的shell）<br>
（可用于文字流的格式、颜色的输出，而输出到file的时候则用另外的方法）

* 技巧38：测试流



## 第6章 文件系统：通过异步和同步的方法处理文件

* 技巧39：读取配置文件
* 技巧40：使用文件描述

|Stream|File descriptor|Description|
|---|---|---|
|stdin|0|标准输入|
|stdout|1|标准输出|
|stderr|2|标准错误|

所谓文件描述就是中间这个数字

* 技巧41：使用文件锁
这个比较复杂（推荐复习）

```javascript
//使用独占标记创建锁文件
fs.open("file.lock", "wx", (err) => {})
//x代表以独占模式打开
```

```javascript
fs.writeFile("file.lock", process.id, { flags: 'wx' }, (err) => {})
```
* 技巧42：递归文件操作
* 技巧43：编写文件数据库
* 技巧44：监视文件以及文件夹

||原理|优缺点|
|---|---|---|---|
|fs.watch|由操作系统通知node进程|性能好<br>不会有遗漏<br>有兼容问题|
|fs.watchFile|轮询文件系统|反之|