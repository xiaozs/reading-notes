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
```npm i -g windows-build-tools```
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
