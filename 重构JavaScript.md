# 《重构JavaScript》
## 第1章 重构是什么？
在不影响软件功能的情况下，提高软件编码质量

## 第2章 你使用哪种JavaScript？
使用的JavaScript环境、框架、库等会对我们的重构产生影响

## 第3章 测试
* 为什么测试
* 测试的多种方法：
    * 手工测试（程序员一遍编码一遍做的那种）
    * 文档化手工测试（对着测试用例一条条过的那种）
    * 批准测试（自动进行一部分，是否通过需要手工确认的那种，显示对比之类的）
    * 端到端测试（也叫集成测试，一般不会用mock，stub）
    * 单元测试
    * 非功能测试
        * 性能测试
        * 可用性测试
        * 游戏测试
        * 安全测试
        * 可访问性测试
        * 本地化测试
* 测试工具
    * 版本控制
    * 测试框架
    * 断言/期望语法库
    * 领域专用库
    * mock/stub库
    * 构建/任务/打包工具
    * 加载器和观察者（观察文件系统变化，自动执行测试文件）
    * 测试运行并行器（使用时要注意被测试代码的副作用）
    * 持续集成服务
    * 覆盖率报告
    * linter
    * 调试器/记录器
    * Staging/QA服务器（模拟生产环境的虚拟机/实例）


