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

## 第3章 Chrome V8 基础
* C++中创建的js对象都不需要我们手动释放。

```
下面是有关于V8内存机制有关的，东西比较多，内容丰富，很推荐重读：
```
* 新生代内存：<br>
区域小、回收频繁<br>
有一个类似于栈指针的指针，生成新对象，指针就增长，到达区域末尾，就会触发回收<br>
使用scavenge回收算法（内存劈两半，一半满了就把存活对象复制到另一半）（只适合新生代这种小内存）<br>
晋升老生代规则：1.经过了2次回收仍然存活；或者2.本次回收存活对象占to空间25%（也就是新生代的1/8）

* 老生代内存：<br>
区域大<br>
Mark-Sweep回收算法（标记、清除）（会产生内存碎片）
Mark-Compact(Mark-Sweep的基础上还进行了对象位移)
惰性清理：先标记、但不急着清除

---

* 隔离实例(Isolate)：看成一个V8实例也行。<br>
在一个实例中创建的对象，不能再别的实例里使用。（这个应该和js单线程有关）<br>
由于我们是在编写扩展，运行我们的扩展的时候v8实例已经存在了，我们只要获取其引用。
```cpp
void Method(const v8::FunctionCallbackInfo<v8::Value>& args) {
    Isolate* isolate = args.GetIsolate();
}
```
* 上下文（Context）：
一个沙箱化的执行上下文（应该就是一个global对象），编写扩展的时候不会用上。
* 脚本（Script）：
```cpp
v8::Local<v8::Context> context = ...;
v8::Local<v8::String> source = 一段js代码;
// 与上下文绑定并编译
v8::Local<v8::Value> result = v8::Script::Compile(context, source).ToLocalChecked();
// 执行脚本
v8::Local<v8::Value> result = script->Run(context).ToLocalChecked();
```

* 句柄（Handle）：<br>
提供了对堆内存中的js对象的应用，<br>
不直接用指针的原因是因为v8的内存管理会对js对象进行移位,<br>
一旦进行移位后，该指针就成野指针了。<br>
js对象一旦没有被句柄引用就会被回收。<br>
    1. 本地句柄（v8::Local）
    2. 持久句柄（v8::Persistent）
    3. 永生句柄（v8::Eternal）
    4. 待实本地句柄
    5. 其它句柄
```cpp
Local<String> str;
str.IsEmpty() //句柄对象本身的函数
str->Length() //通过->得到String*，调用String*的Length()方法
```
