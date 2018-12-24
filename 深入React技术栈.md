# 《深入React技术栈》

## 第1章 初入React世界
介绍了react的一些基础东西，多而杂。

## 第2章 漫谈React

介绍了react一些相关的细节，只挑有意义的：
* 事件：实现了合成事件和事件代理（会导致一些和stopPropagation相关的问题）
* 受控组件和非受控组件：
* css modules
* 组件间通信
* mixin、高等函数（属性代理、反向继承）
* shouldComponentUpdate
* Immutable.js
    1. 降低了“可变”带来的复杂度（不可变性带来）
    2. 节省内存
    3. 撤销/重做，复制/粘贴，甚至时间旅行这些功能做起来都是小菜一碟
    4. 并发安全
    5. 拥抱函数式编程
* key
* react-addons-perf
* 动画：
    * css动画：局限性（只支持 cubic-bezier 的缓动、只能针对一些特有的 CSS 属性、transform只能共用同一个缓动函数）
    * js动画
    * React Transition的实现原理，拦截子组件的增删、并且触发相应的钩子，在钩子里面进行动画操作，操作结束后调用done，子组件的原操作被触发。
* 自动化测试：<br>
    * Jest框架的插件react-addons-test-utils拥有的功能：<br>
    (和《React设计模式与最佳实践》第10章里面的笔记的讨论相差无几)
        1. 模拟触发事件
        2. 渲染 React 组件到文档中
        3. 从渲染的 DOM 树中查找含有 class 的节点
        4. 从渲染的 DOM 树中找到指定组件的节点

    * Enzyme框架提供了不同测试方式：<br>
        * shallow：浅渲染，只会渲染本组件内容，引用的外部组件不会渲染
        * render：用js来模拟dom环境（因为运行测试的是node环境）
        * mount：做全渲染，对于测试生命周期时非常有用

## 第3章　解读React源码
对react的一些核心代码的源码进行的评讲。
* Virtual DOM 模型
* 生命周期
* setState
* diff 算法
* React Patch 方法

## 第4章 认识Flux架构模式
Flux的思想、实现什么的前面读的两本书里面都有说到了，就不再重复了。