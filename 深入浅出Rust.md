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

## 第2章 变量和类型