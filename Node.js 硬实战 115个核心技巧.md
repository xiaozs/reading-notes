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

