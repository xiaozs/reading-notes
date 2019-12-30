# 《C#并发编程经典实例》

## 第1章 并发编程概述

介绍了以下术语：
* 并发
* 多线程
* 并行处理（独立意味着没有共享状态）
    1. 数据并行：有大量的数据需要处理，并且每一块数据的处理过程基本上是彼此独立的<br>
    （Parallel.ForEach、PLINQ的AsParallel接口）
    2. 任务并行：需要执行大量任务，并且每个任务的执行过程基本上是彼此独立的<br>
    任务并行可以是动态的，如果一个任务的执行结果会产生额外的任务，这些新增的任务也可以加入任务池
* 异步编程
* 响应式编程（Rx没有什么好说的）
* 数据流（看起来跟Rx挺像）
* 并发编程的集合（七周七并发模型## 第4章）
    1. 并发集合（有快照功能，可在修改的同时被枚举）
    2. 不可变集合

**现代设计**：

1. 大多数并发编程技术有一个共同点：它们本质上都是函数式的。
2. 应该尽可能避免让代码依赖于全局（或共享）变量，<br>
或者修改全局（或共享）数据结构。<br>
不论是异步方法、并行任务、Rx操作还是数据流块。

## 第2章 异步编程基础
这个跟es7的await/async配合Promise差不多，<br>
唯一搞不懂的是上下文延续<br>
还有一个就是异步和并行、或并发有什么关系

## 第3章 并行开发的基础
* Parallel.ForEach、PLINQ的AsParallel接口
* 上面的都是对task对象的封装，所以他们能做的task都能做，task的功能需要深入研究

**对比**<br>
在并发编程中，Task类有两个作用：作为并行任务，或作为异步任务。
* 并行任务：
    1. 可以使用阻塞的**成员函数**
    2. 常用AttachedToParent 来建立任务间的父子关系
* 异步任务：
    1. 避免使用阻塞的**成员函数**
    2. 用await来建立父子关系


## 第4章 数据流基础
没看懂这个东西和一般的函数式有什么区别

## 第5章 Rx基础
唯一有意思的地方是通过Observable.ObserveOn方法把回调拉倒别的进程里面执行这一点<br>
其余的建议看《深入浅出Rxjs》

## 第6章 测试技巧
Rx通过**调度器**（scheduler）来实现虚拟时间，使得异步测试运行时间大大减少

## 第7章 互操作
Parallel类和并行LINQ利用线程池做并行处理。

await Rx.LastAsync      //取事件流的最后/第一/所有结果，
await Rx.FirstAsync     //其实就是监听其compelete,
await Rx.toList         //并返回结果

## 第8章 集合
* 不可变集合：是永远不会改变的集合
* 线程安全集合：是可同时被多个线程修改的可变集合（用的是事务）
* 生产者/消费者集合：是一种可变集合，这类集合的设计带有特殊的目的：支持（可能有多个）生产者向集合推送项目，同时支持（可能有多个）消费者从集合取走项目。（固定了集合的大小，当集合已经满了的时候会阻塞生产者）

## 第9章 取消
```C#
async Task IssueCancelRequestAsync()
{
    var cts = new CancellationTokenSource();
    var task = CancelableMethodAsync(cts.Token);

    // 这里，操作在正常运行。

    // 发出取消请求。
    cts.Cancel();

    // （异步地）等待操作结束。
    try
    {
        await task;
        // 如运行到这里，说明在取消请求生效前，操作正常完成 。
    }
    catch (OperationCanceledException)
    {
        // 如运行到这里，说明操作在完成前被取消。
    }
    catch (Exception)
    {
        // 如运行到这里，说明在取消请求生效前，操作出错并结束。
        throw;
    }
}

public task CancelableMethodAsync(CancellationToken cancellationToken)
{
    for (int i = 0; i != 100000; ++i)
    {
        Thread.Sleep(1); // 这里做一些计算工作。
        cancellationToken.ThrowIfCancellationRequested();
    }
    return 42;
}
```

## 第10章 函数式OOP
一些和async/await使用相关的小用例，和函数式没有什么关系

## 第11章 同步
如果下面三个条件都满足，就需要用同步来保护共享的数据。
* 多段代码正在并发运行。
* 这几段代码在访问（读或写）同一个数据。
* 至少有一段代码在修改（写）数据。

关于锁的使用，有四条重要的规则。
* 限制锁的作用范围。
* 文档中写清锁保护的内容。
* 锁范围内的代码尽量少。
* 在控制锁的时候绝不运行随意的代码。

```C#
class MyClass
{
    // 这个锁会保护_value。
    private readonly object _mutex = new object();

    private int _value;

    public void Increment()
    {
        // 本节介绍的lock语句与await并不兼容
        lock (_mutex)
        {
            _value = _value + 1;
        }
    }
}
```

* 异步锁
```C#
class MyClass
{
    // 这个锁保护_value。
    private readonly SemaphoreSlim _mutex = new SemaphoreSlim(1);

    private int _value;

    public async Task DelayAndIncrementAsync()
    {
        await _mutex.WaitAsync();
        try
        {
            var oldValue = _value;
            await Task.Delay(TimeSpan.FromSeconds(oldValue));
            _value = oldValue + 1;
        }
        finally
        {
            _mutex.Release();
        }
    }
}
```

* 阻塞信号
用于从一个线程发送信号给另一个线程
```C#
class MyClass
{
    private readonly ManualResetEventSlim _initialized =new ManualResetEventSlim();

    private int _value;

    public int WaitForInitialization()
    {
        _initialized.Wait();
        return _value;
    }

    public void InitializeFromAnotherThread()
    {
        _value = 13;
        _initialized.Set();
    }
}
```

* 异步信号
需要在代码的各个部分间发送通知，并且要求接收方必须进行异步等待。
```C#
class MyClass
{
    private readonly AsyncManualResetEvent _connected =new AsyncManualResetEvent();

    public async Task WaitForConnectedAsync()
    {
        await _connected.WaitAsync();
    }

    public void ConnectedChanged(bool connected)
    {
        if (connected)
            _connected.Set();
        else
            _connected.Reset();
    }
}
```

* 限流
有一段高度并发的代码，由于它的并发程度实在太高了，需要有方法对并发性进行限流<br>
一个锁可以理解为每次只允许一个的限流。
```C#
IPropagatorBlock<int, int> DataflowMultiplyBy2()
{
    var options = new ExecutionDataflowBlockOptions
    {
        MaxDegreeOfParallelism = 10
    };

    return new TransformBlock<int, int>(data => data * 2, options);
}
```
```C#
// 使用PLINQ
IEnumerable<int> ParallelMultiplyBy2(IEnumerable<int> values)
{
    return values.AsParallel()
        .WithDegreeOfParallelism(10)
        .Select(item => item * 2);
}
```
```C#
// 使用Parallel类
void ParallelRotateMatrices(IEnumerable<Matrix> matrices, float degrees)
{
    var options = new ParallelOptions
    {
        MaxDegreeOfParallelism = 10
    };
    Parallel.ForEach(matrices, options, matrix => matrix.Rotate(degrees));
}
```
```C#
async Task<string[]> DownloadUrlsAsync(IEnumerable<string> urls)
{
    var httpClient = new HttpClient();
    var semaphore = new SemaphoreSlim(10);
    var tasks = urls.Select(async url =>
    {
        await semaphore.WaitAsync();
        try
        {
            return await httpClient.GetStringAsync(url);
        }
        finally
        {
            semaphore.Release();
        }
    }).ToArray();
    return await Task.WhenAll(tasks);
}
```

## 第12章 调度
代码必须在某个线程上运行。<br>
**调度器**是一个确定代码运行地点的对象<br>
建议大家尽量不要指定调度器，系统默认的调度器通常是最合适的
* 任务调度器
```C#
void RotateMatrices(IEnumerable<IEnumerable<Matrix>> collections, float degrees)
{
    var schedulerPair = new ConcurrentExclusiveSchedulerPair(
        TaskScheduler.Default, maxConcurrencyLevel: 8);
    TaskScheduler scheduler = schedulerPair.ConcurrentScheduler;
    ParallelOptions options = new ParallelOptions { TaskScheduler = scheduler };
    Parallel.ForEach(collections, options,
        matrices => Parallel.ForEach(matrices, options,
            matrix => matrix.Rotate(degrees)));
}
```

## 第13章 调度
一些乱七八糟的使用案例