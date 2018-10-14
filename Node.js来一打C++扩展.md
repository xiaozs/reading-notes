# 《Node.js来一打C++扩展》

## 第1章 Node.js的C++扩展前驱知识储备
```
几乎都是和node相关的一些基本知识
这里只挑重点记录：
```
* node-gyp：node的C++扩展构建工具。
* 依赖```npm i -g windows-build-tools```（不要小看它只是一条命令，下载很多东西）
* ```node-gyp install``` 下载node的c++头文件，一个用户执行一次就够了
* ```node-gyp configure``` 生成项目文件（这个命令依赖一个binding.gyp文件，不知道是怎么设置的
* ```node-gyp build``` 构建
* ```node-gyp clean``` 清除
* ```node-gyp rebuild``` = clean + configure + build

## 第2章 C++模块原理简析
```
介绍了用c++写模块的理由：
```
* 性能要求
* 包装已有的c++模块给node调用

```
还有模块加载相关的原理
（看不太懂，也记不住）
```