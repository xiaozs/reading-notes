# 《编写可测试的JavaScript代码》
## 第1章 可测试的JavaScript

||为什么|是什么|如何|
|---|---|---|---|
|可测试|测试这件事始终要有人做|容易被测试|边写代码边写测试|
|可维护|不用担心越改越错|修改了不会引起其它错|短小|
|可理解|为了上面2点|短小|还是短小|



## 第2章 复杂度
可以让函数保持最小代码量的一个方法是
让命令（Command）和查询（Query）保持分离。
命令函数表示做什么（do something），
而查询函数则表示返回什么（return something）


```
介绍了圈复杂度相关知识：
```
生成代码的**圈复杂度**可以使用像jsmeter、Jscheckstyle这样简单的命令行工具

|圈复杂度|修改出错概率|
|:---:|:---:|
|1-10|5%|
|20-30|20%|
|>50|40%|
|>100|60%|


```
扇入、扇出：
```
1. 高扇入和扇出的代码，可能表示一个函数正在尝试做太多事情，应该避免
2. 高扇入和扇出，可以判定出系统的压力点（stress points），维护这些函数将会非常困难，因为它们关联太多的系统其他部分
3. 它们不够精细（inadequate refinement），这意味着函数需要重构，因为它太大且要做的事情太多，或者缺少抽象层才导致该函数的高扇入和高扇出。

```
有一段对于耦合与依赖注入的讨论：

1. 通常来说依赖注入要使用工厂或手动生成对象来进行注入
2. 这就相当于把皮球从一个地方踢到了另一个地方
   （从对象内部耦合生成 -> 工厂、手动）
3. 这有什么不同呢？
   这允许我们随意换一个不同的皮球，
   而这个动作通常是在程序启动时发生。
4. 而皮球可以换成mock有利于我们测试。
```

## 第3章 基于事件的架构

```
总体上来说，这一章是在向我们介绍通过事件发射器来进行模块解耦，解耦后的模块能屏除其它模块的影响，更容易测试了。
```

## 第4章 单元测试

编写好的单元测试：
* 隔离：单元测试应该只加载所需测试的最小化代码进行测试。为了避免加载外部依赖，我们可以使用模（mock）、桩（stub）以及测试替身（test
double）
* 范围：单元测试的范围——实际要测试的东西——必须很小。（貌似和上面那点重复了）
* 明确函数定义：注释、测试用例

---

正向测试：传入正常的数据，并接收正常的数据反
馈

负向测试：，传入非期望的函数或该函数不想要的参数
进行测试，确保被测试的函数能够正确处理它们

---

依赖项：

使用命令查询分离强调模与桩之间的差异：mock 用于命令，而sub 则用于查询。

* 测试替身：<br>
使用stub 或mock 模拟依赖对象进行测试，以确保外部方法和API 被调用、并判断被调用了多少次、捕获所调用的参数、并返回响应。
在方法如何被调用方面，能记录或捕获相关信息的测试替身，被称为间谍（spy）。

* mock 对象：<br>
用于验证函数能够正确调用外部API。通过引入mock 对象验证被测试函数传递正确的参数给外部对象时的结果。

* stub 对象：<br>
用于向被测试函数返回所封装的值。stub 对象不关注外部对象方法是如何调用的；stub 对象只是返回所选择的封装对象。

* spy 对象：<br>
spy 通常是附加到真正的对象上，并拦截一些方法的调用并返回封装响应或跟踪该方法被调用的次数。如果需要模拟（mock）一个含有“重”操作的对象，并对实际对象进行很简单的轻量级操作的话，spy 是一个很好的选择。

## 第5章 代码覆盖率

```
高代码覆盖率不代表代码都被测试过了。

介绍了一下覆盖率的实现方式。
```

## 第6章 集成测试、性能测试、负载测试
* 集成测试：不会对依赖项进行模拟<br>
    Selenium、CasperJS<br>
    这个东西的原理是，用脚本操纵浏览器，模拟用户操作，进行断言、截图等操作。
* 性能测试：你的应用程序能跑多快<br>
    browsermob-proxy<br>
    在浏览器和系统间，建立一个中间层，拦截访问，生成HAR文件（瀑布图）<br>
* 负载测试：你的应用程序可以处理多少个应用程序<br>
    nodeload<br>
    通过nodeload像服务发动并发请求，观察服务的返回速度，观察后台的监察程序

* 跟踪资源利用率：
    * 客户端跟踪：chrome调试程序
    * 服务器端跟踪：《Node.js调试指南》中有更详细的介绍

    
## 第7章 调试
* 浏览器内调试：没啥好说的
* Node.js调试：没啥好说的
* 远程调试：chrome的那个远程调试协议，Selenium就是通过这个协议实现的。
* 移动调试：比较老了，不知道还有没有用
* 生产环境调试：这个也没什么


## 第8章 自动化
* 持续集成
* 开发环境自动化
    * 编辑器
    * 单元测试
    * 代码审查
    * 提交钩子：自动格式化、复杂度分析等
* 构建环境自动化：Jenkins
* 部署：回滚与部署是紧密相连的。应该准备好尽快切换回旧代码——因为任何东西都有可能会出错。<br>
    理想情况下，新旧代码只是一个路径连接符——升级的时候将旧代码也保留在服务器上。