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



## 第7章 网络：Node真正的“Hello, World”
* 技巧45：创建TCP服务端和客户端
```javascript
var net = require("net");
var server = net.createServer(client => {   //client是一个socket
    client.on("end", () => {

    })
    client.write(content);
    client.pipe(stream)
})
server.listen(port, callback);
```

* 技巧46：使用客户端测试TCP服务器
```javascript
//这个才是客户端
var client = net.connect(port);
client.on("data", data => {});
client.on("end", callback);
client.end();
```

* 技巧47：改进实时性低的应用
```javascript
//socket利用Nagle算法做了缓冲，可能会导致延迟，这个api用来关闭
socket.setNoDelay();

//没有相连客户端的时候退出脚本
server.unref();
```

* 技巧48：通过UDP传输文件
```javascript
//client
var dgram = require("dgram");
var socket = dgram.createSocket("udp4");
socket.send(buffer, 0, buffer.length, port, remoteIP, callback);

//server
var dgram = require("dgram");
var socket = dgram.createSocket("udp4");
socket.on("message", callback);
socket.bind(port);
```



* 技巧49：UDP客户端服务应用
描述了一些UDP相关知识：
    1. UDP是单工的，
    2. 可以通过同时连接2个UDP来达到双工效果
    3. 因为客户端没有bind端口，所以可以启动多个客户端从同一个端口发信。



* 技巧50：HTTP客户端和服务器
```javascript
var http = require("http");
var server = http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.write(content);
    res.end();
});
server.listen(port, callback);


var req = http.request({ port }, res => {
    res.headers;
    res.statusCode;
    res.on("data", data => {

    })
})

```

* 技巧51：重定向
* 技巧52：HTTP代理
* 技巧53：创建DNS请求<br>
这个表不知道是怎么用的，总之先记录一下：

|type|method|description|
|---|---|---|
|A|dns.resolve|一个A记录存储IP地址。它可以有一个关联的生存时间（TTL）来表示这个记录多久需要进行更新|
|TXT|dns.resolveTxt|文本值可以用于在DNS上构建的其他服务的额外特性|
|SRV|dns.resolveSrv|服务记录定义服务的定位数据，通常它会包括主机名称和端口号|
|NS|dns.resolveNs|用于指定域名服务器|
|CNAME|dns.resolveCname|相关的域名记录，设置为域名而不是IP地址|



```javascript
var dns = require("dns");
dns.lookup("www.manning.com", (err, address) => {});
dns.resolve("www.manning.com", (err, address) => {}); //这个address会是一个数组
```


* 技巧54：一个加密的TCP服务器
```javascript
//server
var fs = require("fs");
var tls = require("tls");

var options = {
    key: fs.readFileSync("server.pem"),         // 私钥
    cert: fs.readFileSync("server-cert.pen"),   // 公钥
    ca: [fs.readFileSync("client-cert.pen")],   // 客户端验证证书
    requestCert: true
};

var server = tls.createServer(options, cleartextStream => {
    cleartextStream.authorized  //服务器是否能够验证证书
});

server.listen(port, callback);


//client
var fs = require("fs");
var os = require("os");
var tls = require("tls");

var options = {
    key: fs.readFileSync("server.pem"),         // 私钥
    cert: fs.readFileSync("server-cert.pen"),   // 公钥
    ca: [fs.readFileSync("client-cert.pen")],   // 客户端验证证书
    servername: os.hostname()                   // 主机名称作为服务器名称
};

var cleartextStream = tls.connect(port, options, callback);
cleartextStream.authorized
cleartextStream.setEncoding("utf8");
cleartextStream.on("data", callback);
```

* 技巧55：加密的web服务器和客户端

```javascript
//server
var fs = require("fs");
var https = require("https");

var options = {
    key: fs.readFileSync("server.pem"),         // 私钥
    cert: fs.readFileSync("server-cert.pen"),   // 公钥
    ca: [fs.readFileSync("client-cert.pen")],   // 客户端验证证书
    requestCert: true
};

var server = https.createServer(options, (req, res) => {
    req.socket.authorized  //服务器是否能够验证证书
});

server.listen(port, callback);


//client
var fs = require("fs");
var os = require("os");
var https = require("https");

var options = {
    key: fs.readFileSync("server.pem"),         // 私钥
    cert: fs.readFileSync("server-cert.pen"),   // 公钥
    ca: [fs.readFileSync("client-cert.pen")],   // 客户端验证证书
    servername: os.hostname(),                  // 主机名称作为服务器名称
    port,
    method: "GET"
};

var req = https.request(options, res => {
    res.on("data", callback);
})
req.end();
req.on("error", callback)
```



## 第8章 子进程：利用Node整合外部应用程序

|methods|description|
|---|---|
|spawn|执行外部程序，并需要提供一组参数，以及一个在进程退出后的输入输出和事件的数据流接口|
|execFile|执行外部程序，并需要提供一组参数，以及一个在进程退出后的缓冲输出的回调|
|exec|在一个命令行窗口中执行一个或者多个命令，以及一个在进程退出后的缓冲输出的回调|
|fork|在一个独立的进程中执行一个Node模块，并且需要提供一组参数，以及一个类似spwn方法里的数据流和事件式的接口，同时设置好父进程和子进程之间的进程间通信。|



* 技巧56：执行外部应用程序

```javascript
var cp = require("child_process");
//[]中放置参数可以防止注入，下同
cp.execFile("echo", ["hello", "world"], (err, stdout, stderr) => {
    err.code    //不为0时，执行失败
    stderr      //具体失败原因
})
```

* 技巧57：流和外部应用程序

```javascript
var cp = require("child_process");
var child = cp.spawn("echo", ["hello", "world"])
child.on("error", callback)
child.stdout.pipe(stream);
child.stderr.pipe(stream);
```

* 技巧58：在shell中执行命令
```javascript
var cp = require("child_process");
var child = cp.exec("echo", ["hello", "world"], (err, stdout, stderr) => {
    err.code    //不为0时，执行失败
    stderr      //具体失败原因
})
```

* 技巧59：分离子进程
```javascript
var cp = require("child_process");
var fs = require("fs");

var outFd = fs.openSync("./out.file", "a");
var errFd = fs.openSync("./err.file", "a");

var child = cp.spawn("app", [], {
    detached: true,     // 父进程挂了，子进程不会跟着挂

    //子进程的stdio，
    //默认是["pipe", "pipe", "pipe"]，意思是用父进程的stdio，
    //由于用了父进程的stdio，在子进程退出之前父进程都不会退出
    //这样配置过后，父进程就不会等待子进程执行完毕才退出
    stdio:["ignore", outFd, errFd]  
})

//移除子进程在父进程中的引用
child.unref();

//三者缺一不可
```

* 技巧60：执行Node程序
Windows平台下：
1. 
```
@echo off
node "app.js" %*
```
UNIX平台下：
1. #!/usr/bin/env node
2. chmod +x app.js


* 技巧61：Forking Node模块
```javascript
//child
process.on("message", (msg) => {
    process.send(msg);
})

//parent
var cp = require("child_process");
var child = cp.fork("./child");
child.on("message", msg => {

})
child.send(msg);

//断开子进程
child.disconnect();
```



* 技巧62：运行作业
* 技巧63：同步子进程（上面方法的同步版）


## 第9章 网络：构建精简的网络应用
* 技巧64：快速的静态网站服务器
* 技巧65：在Node中使用DOM
* 技巧66：在浏览器端使用Node模块
* 技巧67：Express路由分离
* 技巧68：自动重启服务器<br>
介绍了nodemon包
* 技巧69：配置web应用<br>
介绍了nconf包
* 技巧70：优雅地处理错误
```javascript
app.get("path", (req, res, next) => {
    //出错时应该直接用next返回err
    return next(err);
});

//异常处理的中间件有4个参数，
//应该是用了Function.prototype.length的原因
app.use((err, req, res, next) => {
    //记录异常，
    //返回错误页面/响应
})
```

* 技巧71：RESTful web应用
* 技巧72：使用自定义的中间件
* 技巧73：使用事件进行解耦
* 技巧74：使用WebSockets来处理sessions
```javascript
var express = require("express");
var WebSocketServer = require("ws").Server;
var parserCookie = express.cookieParser("some secret");
var MemoryStore = express.session.MemoryStore;
var store = new MemoryStore();

var app = express();
var server = app.listen(port);

app.use(parseCookie);
//session存到store里
app.use(express.session({ store }));
app.get("/random", (req, res) => {
    //session内容设置
    req.session.random = data;
    req.send(200);
});

//webSocketServer依托于express，实现原理不明
var webSocketServer = new WebSocketServer({ server });
webSocketServer.on("connection", ws => {
    ws.on("message", (data, flags) => {
        parseCookie(ws.upgradeReq, null, err => {
            //从协议升级时的请求报文的cookie中获取sessionId
            var sid = ws.upgradeReq.signedCookies["connect.sid"];

            //通过sid获取对应session
            store.get(sid, (err, loadedSession) => {
                //前面"/random"里面设置的值
                loadedSession.random
            })
        })
    })
})
```
* 技巧75：升级Expressin 3 到 4
* 技巧76：测试路由（这里其实是对Session进行测试，名不副实）
* 技巧77：为中间件注入创建seams（说的是桩、spy之类的东西）
* 技巧78：测试依赖远程服务的应用（用mock来替代真正的远程）

## 第10章 测试：编写健壮代码的关键

* 技巧79：用内置模块写测试
* 技巧80：编写验证异常的测试
* 技巧81：创建自定义的断言
* 技巧82：使用一个测试装置（就是测试框架）组织测试
* 技巧83：使用Mocha编写测试
* 技巧84：使用Mocha测试web应用
* 技巧85：万能测试协议（TAP）（这玩意的api和qunit多少有点像）
* 技巧86：持续集成（代码提交到服务器时，对其进行自动测试，一旦有问题给组员发送邮件或信息）
* 技巧87：数据库装置
//todo


## 第11章 调试：用于发现和解决问题
和异常相关的需要注意的点：
1. throw只能用于同步
2. 抛出的异常都应该继承自Error，否则打印不了异常栈
3. 不要在node内置函数的callback里面抛出异常
* 技巧88：处理未捕获的异常
    1. process.on("uncaughtException", cb)
    2. 使用domain
* 技巧89：检查我们的Node代码

推荐看《Node.js调试指南》：
* 技巧90：使用Node的内置调试器
* 技巧91：使用Node Inspector
* 技巧92：对Node应用进行性能分析
* 技巧93：内存泄漏的调试（heapdump生成内存快照，用chrome来查看）
* 技巧94：使用REPL来检测运行中的程序（调试运行中的程序）
```javascript
var net = require("net");
var repl = require("repl");
//可以通过客户端和这个服务器进行交互
//一旦连接了会给客户端提供一个js的repl
net.createServer(socket => {
    var r = repl.start({
        input: socket,
        output: socket,
        useGlobal: true //可以设置全局变量
    });
    r.on("exit", () => socket.end());
    r.context.xxx = xxx;
    //在repl中暴露出xxx
}).listen(port);
```

* 技巧95：跟踪系统调用<br>
（和系统相关，在linux下用strace命令可以生成相关进程的系统调用记录）

