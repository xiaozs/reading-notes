# 《深入理解JavaScript特性》
## 第1章 ECMAScript和JavaScript的未来
介绍了js的发展历史

## 第2章 ES6基础
简单的介绍了一些es6的简单特性

## 第3章 类、符号、对象和装饰器
* 类：没啥
* 符号（Symbol）
    * 本地符号（Symbol()）
        * for..in、Object.keys 和 Object.getOwnPropertyNames 均无法获取符号属性
        * 符号属性不会被序列化
        * 好处：只要修改符号属性值或利用了该行为的代码，<br>
        就能增强或修改对象的行为。<br>
        此外，使用符号作为属性无须担心与语言的新特性发生冲突
    * 全局符号注册表（Symbol.for(key)、Symbol.keyFor(symbol)）：全局符号注册表在所有代码域中是共享的
    * 众所周知的符号：语言自带的，够在不破坏现有代码的情况下扩展语言（它们跨代码域共享但不在全局注册表中）
* 对象：
    * Object.assign
    * Object.is
    * Object.setPrototypeOf
* 装饰器

