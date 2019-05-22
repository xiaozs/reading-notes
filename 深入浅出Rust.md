# 《深入浅出Rust》

## 第1章 与君初相见
* rustc.exe: 编译器
* cargo.exe: 包管理器
* cargo-fmt.exe, rustfmt.exe: 源代码格式化工具
* rust-gdb.exe, rust-lldb.exe: 调试器
* rustdoc.exe: 文档生成器
* rls.exe, racer.exe: 代码提示工具
* rustup.exe: 管理这套工具链下载更新的工具。

* 代理:
```
export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup
````
* $HOME/.cargo目录下创建一个名为config的文本文件:
```
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"
```
* 安装vsms
```
npm i -g windows-build-tools
```

## 第2章 变量和类型
* const fn: 的运算发生在编译时。
* 还有一堆关于 溢出、NaN、Infinity 的讨论


## 第3章 语句和表达式
* if-else 可以返回值，所以没有三目运算符
* loop 可用通过break返回值

## 第4章 函数
* !类型： 异常或死循环函数的返回类型
* std::env::args() //获取命令行参数的方法，和node有点像

## 第5章 trait
trait 是一个类似于interface的东西，有一些不同之处：
* 可以有可override的默认实现
* 可以通过调用方法来区分来自不同trait的相同名称的接口
* 可以规定静态方法
* 某些情况下可以像C#扩展方法那样用
* 等

## 第6章 数组和字符串
没有什么特别的，介绍了一下 边界检查、切片 的内容

## 第7章 模式解构
* match 语句要覆盖所有分支，如果上游进行了修改，可能会破坏下游
* match 也是表达式

## 第8章 深入类型系统
通过介绍代数类型系统来引出Never Type，说明Never Type的作用:
* 可以使得泛型代码兼容diverging function
* 更好的死代码检查
* 可以用更好的方式表达“不可能出现的情况”

## 第9章 宏
宏的使用比较复杂，而且api还没有固定。

* 实现编译阶段检查
* 实现编译期计算
* 实现自动代码生成
* 实现语法扩展

## 第10章 内存管理基础
说了一些传统的编程语言（C\C++）的内存管理问题。

## 第11章 所有权和移动语义

* 所有权：
    * 每个值在Rust中都有一个变量来管理它，这个变量就是这个值、这块内存的所有者
    * 每个值在一个时间点上只有一个管理者
    * 当变量所在的作用域结束的时候，变量以及它代表的值将会被销毁


首先rust是没有像C#那样的引用语义和值语义的，因为同一类型的对象既可以在栈上生成，又可以在堆上生成。

* 移动语义：对一个变量进行赋值时，新变量会代替原变量，接收对象的所有权
* 复制语义：同上过程，会复制一个新对象，新变量会拥有新变量的所有权

* Box类型：拥有所有权的指针，永远执行的是move语义。

* Copy：简单内存复制，Rust中只有POD（C++语言中的Plain Old Data）类型才有资格实现Copy trait。<br>在Rust中，如果一个类型只包含POD数据类型的成员，并且没有自定义析构函数，那它就是POD类型。
* Clone：对于POD，是简单内存复制；对于Box类型，clone执行的是“深复制”；而对于Rc类型，clone做的事情就是把引用计数值加1。

* 析构函数：
    * 主动析构
    * 析构标记：rust是通过编译器在函数中外插变量来保存对象的析构情况的，不是在对象中有一个隐藏字段

