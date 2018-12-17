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