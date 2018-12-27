# 《JavaScript测试驱动开发》

## 第1章 自动化测试让你重获自由
集中在UI层的测试有很多缺点：
1. 非常脆弱（UI是应用中最容易变动的部分）
2. 过多的活动部分（Selenium这样的工具很复杂）
3. 非常缓慢（这些测试需要运行整个应用，其中包括客户端、服务器端、数据库）
4. 难以编写
5. 无法隔离问题域（出现了问题，但是很难知道具体是哪里的问题）
6. 无法防止在UI层中包含业务逻辑
7. 无法改进设计

## 第2章 测试驱动设计
Arrange-Act-Assert（准备-行动-断言，3-As）模式
* 正向测试：当前置条件满足时，验证代码的结果确实符合预期。
* 反向测试：当前置条件或者输入不符合要求时，代码能优雅地进行处理。
* 异常测试：代码在应该抛出异常的地方正确地抛出了异常

测试要保持：
* 快速
* 自动化
* 独立（测试可以以任意顺序执行，而且你可以选择运行一部分测试或所有测试）
* 可重复

这一章介绍了Mocha、Chai、Karma和Istanbul这些工具

## 第3章 异步测试
通过回调来确认测试的结束，或者是超时后引发失败。<Br>
又或者是测试返回promise。

## 第4章 巧妙处理依赖
这里通过了一个小demo来说明**单一职责原则**在测试中的重要性：
    * 目标明确所以易于测试
    * 依赖较少，容易测试

测试替身：
    * fake：适用于测试但不能用于生产环境的实现
    * stub：被调用时可以快速返回预设数据，用来验证依赖返回（预设的）结果后代码的行为。
    * mock：也可以返回预设数据。但它对交互进行跟踪，如调用的次数、调用的顺序。用于帮助验证被测代码和依赖之间的交互。
    * spy：可以代理真实的依赖。当在测试期间与真实的服务交互，而我们又想对交互进行验证或者对选择的部分进行模拟时。

**依赖注入**：依赖在调用时作为参数传递。
```javascript
//Sinon stub、mock、spy

var sandbox;
beforeEach(function() {
    sandbox = sinon.sandbox.create();
});

var aSpy = sandbox.spy(existingFunction);
expect(aSpy.called).to.be.true;
express(aSpy).to.have.been.calledWith('magic');

sandbox.stub(util, 'alias')
       .withArgs('Robert')
       .returns('Bob');

var aMock = sandbox.mock(util)
                   .expects('alias')
                   .withArgs('Robert');
aMock.verify();

afterEach(function() {
    sandbox.restore();
});
```