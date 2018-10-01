# 《JavaScript框架设计》

## 第1章 种子模块

```
介绍了js的 类型判断 domReady 无冲突处理 等东西
旧时代遗留太多，对兼容开发有一定用处。
```

## 第2章 语言模块

```
貌似还是基础设施建设相关

1. 字符串相关的方法：我觉得就byteLen，和防xss转义有点用
2. 数组相关扩展，是大部分函数式库会做的事情
3. 函数的扩展，同上
4. 日期的扩展，有一些库moment.js之类的进行处理
```

## 第3章 浏览器嗅探与特征侦测

```
浏览器嗅探适合那些统计工具来用，其余不推荐。
```
```
// 事件的支持侦测（只支持dom0事件，以"DOM"开头的事件侦测不到）

$.eventSupport = function(eventName, el) {
    el = el || document.documentElement
    eventName = "on" + eventName;

    var ret = eventName in el;
    if (el.setAttribute && !ret) {
        el.setAttribute(eventName, "");
        ret = typeof el[eventName] === "function";
        el.removeAttribute(eventName);
    }
    el = null;
    return ret;
}
```

```
除了以上的那个事件侦查以外还介绍了一个
样式浏览器前缀变换的函数，

和其它一些乱七八遭的。
```

## 第4章 类工厂
```
介绍了一些和继承相关的函数库，
实际上只要掌握好了js继承相关的知识，就没有什么好看的了，

毕竟之后还有es6等着我们。
就算想要更加仔细的了解继承相关的细节，还是更加推荐那部《你所不知道的Javascript》


还介绍了一些和描述符相关的api，
我觉得重要的还是setter，getter的设置。
```


## 第5章 选择器引擎
```
现代浏览器querySelector的api已经原生实现的选择器引擎了，
大行其道的mvvm框架也降低了直接操纵dom的需求，
所以选择器引擎可能接下来用处就小了。

姑且一看。
其中一些dom相关api建议看《dom启蒙》。
和jquery选择器实现哲学相关的可以看《扩展jQuery》
```

## 第6章 节点模块

```
ie下的innerHTML存在以下问题：
```
* 