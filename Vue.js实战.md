# 《Vue.js实战》
## 第1章 初识Vue.js
简单的介绍了一下Vue，特别强调了其渐进式（就是应用的一部分用vue实现，其他照旧）

## 第2章 数据绑定和第一个Vue应用
提供了一个简单的demo用于说明一些template语法。

## 第3章 计算属性
讨论了计算属性的用法和它拥有的一些特性：
    * 缓存

## 第4章 v-bind及class与style绑定
题目说明一切

## 第5章 内置指令
v-cloak：这个的原理很简单，在整个页面是js生成的时候完全没有用。
v-once：这个指令涉及到render的一个"静态内容"的概念，其实现原理有待考察
v-if、v-else-if、v-else：跳过
v-show：跳过
v-for：跳过
v-bind：
    * 修饰符（修饰符极大的丰富了v-bind指令的使用方式，这个很值得参考（或者可以考虑把这些修饰放在修饰器里？））

## 第6章 表单与v-model
v-model也有修饰符，用于对同步时机、同步内容的修改上。

## 第7章 组件详解
* 组件通信：
    * v-modal、v-bind、
    * $broadcast、$dispatch（已废除）
    * $parent、$children
    * $on、$emit

* slot：（对比起react，这个可以实现的功能更丰富一点）
    * 单个slot
    * 具名slot
    * 作用域插槽（相当于react的函数子组）

* 高级用法：
    * 递归组件
    * 动态组件
    * 异步组件