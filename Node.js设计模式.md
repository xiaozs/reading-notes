# 《Node.js设计模式》

## 第1章 欢迎来到Node.js平台

```
我终于搞懂了什么是Reactor模式了，说的就是js的那一套事件循环。
```

## 第2章 Node.js基础设计模式

```
讲述了node的回调模式：
1. 函数要统一为异步/同步，不能半异步半同步，这一点在那本《JavaScript异步编程：设计快速响应的网络应用》里面也有提到。
2. 谈论了回调模式中的异常捕捉和抛出方式（全部都要手动捕捉和抛出）（这一点如果是用了async函数会有很大的改善）
3. 对于未捕捉到的异常，可以用process.on("uncaughtException",(err)=>{})来捕捉

模块加载的模式：
1. cmd的实现原理，在《深入浅出Node.js》里面也说过。
2. module.exports和exports的区别，这个也在上面那本里面说过了。
3. require函数是同步的。（我记得这个被作为面试题，被某位大佬在zhihu上提出过）（答案是：在代码加载这一块上异步带来的优点比不过它带来的复杂性）
4. require模块的解析方式（require.resolve方法）
5. 模块缓存（require.cache）
6. 讨论了循环应用带来的问题（不知道为什么这个竟然没有抛出）
7. new.target语法的作用，new.target用于判断是否使用new来调用的当前函数

观察者模式：
1. 和之前讨论的一样，回调中的异常要自己手动捕获、抛出。
2. 自带的EventEmitter如果不绑定error事件，一旦遇到没捕获异常会退出程序
3. 讨论了同步、异步事件，其实就是是否setTimeout来调用emit方法的不同。
4. EventEmitter与回调（讨论了事件和回调的不同：取异步操作的结果时用回调，其它用事件；事件一对多，事件还可以对多种类型事件，回调一对一；）
```

```
总体来说这一章书的收获还是挺大的。
```

## 第3章 异步控制流模式之回调函数

```
如何避免地狱回调，就是把匿名的回调函数，起个名，拉其它地方就好了。
用es6就更简单了。

这里还有一种手写的方法，我也是醉了。
1. 顺序执行
2. 并行执行
3. 有限制的并行执行

介绍了用async库来重写的上面3个模式
```

## 第4章 异步控制流模式之ES2015+

```
介绍了用Promise来重写的上面3个模式

接受了generator相关语法、api
用thunk配合generator重写了上面3个模式，
这一套东西实在是太复杂了，之后还要重新复习一遍thunk配合generator的原理。

最后是async/await，
这一套的实现原理其实和上面的thunk配合generator是一致的，
把thunk换成Promise，用语法关键字来避免generator的api的显式调用，
async关键字为转译器表明了generator转换的地方。
```

## 第5章 流编程
```
介绍了缓冲和流的区别

流stream是EventEmitter类的实例。
抽象类：
stream.Readable
stream.Writable
stream.Duplex
stream.Transform
```