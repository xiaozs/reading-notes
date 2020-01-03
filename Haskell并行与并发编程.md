# 《Haskell并行与并发编程》
## 第1章 绪论
并行性和并发性的概念


## 第2章 并行基础：Eval Monad
```haskell
runEval $ do
   a <- rpar(f x)
   b <- rpar(f y)
   rseq a
   rseq b
   return (a, b)
```
类似于
```javascript
async () => {
    let aPromise = (async ()=> f(x))();
    let bPromise = (async ()=> f(y))();

    let a = await aPromise;
    let b = await bPromise;

    return (a, b);
}()
```

同时也介绍了haskell的惰性求值系统的原理


## 第3章 求值策略
函数式的策略模式



## 第4章 数据流并行：Par Monad
类似于channel和go块<br>
可以参考《七周七并发模型》第6章 通信顺序进程



## 第5章 Repa数据并行编程
使用Repa库，以DSL的方式编写CUDA或OpenCL代码。



## 第7章 并发基础：线程和MVar
MVar是一个对于锁的抽象<br>
可用于共享状态



## 第8章 重叠I/O
通过上一章的内容来实现异步的方式<br>
异常的处理方式<br>
不同于js的异步，这里的异步是会阻塞主线程的。

## 第9章 线程的取消和超时