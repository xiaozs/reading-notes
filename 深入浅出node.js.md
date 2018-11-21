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
* 把登录口令放在queryString的方法：没有口令就生成一个，然后重定向到带口令的对应地址。
* cookie加密的原理
    1. 攻击者会猜测cookie的生成方法，来碰撞已有用户的cookie<br>
    解决办法：在原有的cookie后面，加入以cookie、sercet、私钥加密成的hash，由于攻击者不知道后两者，所以无法生成猜测的cookie
    2. 攻击者获取了cookie，<br>
    解决办法：在用户进行访问的时候，set-cookie时，把用户对应的ua、ip等信息加密进cookie里面，由于攻击者的这些条件可能对应不上，就算获取到了cookie也没有用

* 数据上传与安全：
    1. 内存限制（限制报文大小）

* 渲染：
    1. Bigpipe技术：
        * 先把框架模板发送到前端，之后的内容用\<script>来填充
        * 一整个页面都在一个http请求里面完成
