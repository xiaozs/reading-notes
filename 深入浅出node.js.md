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


