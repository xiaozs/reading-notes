# 《WebAssembly标准入门》
## 第0章 WebAssembly诞生背景
asm.js：<br>
JavaScript 子集，仅包含可以预判变量类型的数值运算，有效地避免了JavaScript 弱类型变量语法带来的执行效能低下的问题。<br>
对比起WebAssembly，因为是js文本，所以没有兼容问题。

## 第1章 JavaScript语言基础
* 二进制数组：
    1. ArrayBuffer：代表内存中一段空间，要操作该内存空间必须通过基于其创建的TypedArray 或DataView 进行。
    2. TypedArray：Uint8Array、Float32Array 等9 种二进制类型数组的统称，TypedArray 的底层是ArrayBuffer 对象，通过它们可以读写底层的二进制数组。
    3. DataView：用于处理类似C 语言中结构体类型的数据，其中每个元素的类型可能并不相同。
    * 二进制数组是JavaScript 在处理运算密集型应用时经常用到的特性。<br>
    同时二进制数组也是网络数据交换和跨语言数据交换最有效的手段。


## 第2章 WebAssembly快速入门
* WebAssembly 兼容性
* wabt 工具集（主要是wat和wasm两个格式间的转换）
* 加载代码：

```wasm
(module
    ;;引入js.print函数，该函数有两个32位整形参数
    (import "js" "print" (func $js_print (param i32 i32)))

    ;;引入js.mem作为内存（一个实例只能拥有一个内存对象以及一个表格对象）
    ;;该内存的起始长度至少为1（64kb）
    (import "js" "mem" (memory 1))

    ;;写内存，起始地址为0
    (data (i32.const 0) "hello，world")

    ;;导出hello函数
    (func (export "hello")

        ;;调用$js_print的第一个参数
        i32.const 0
        ;;第二个
        i32.const 1

        ;;调用
        call $js_print
    )
)
```
```javascript

// 创建内存对象初始长度为1
var wasmMem = new WebAssembly.Memory({ initial: 1 });

function printStr(offset, length) {
    var bytes = new Uint8Array(wasmMem.buffer, offset, length);
    var string = new TextDecoder('utf8').decode(bytes);
    console.log(string);
}

var importObj = { js: { print: printStr, mem: wasmMem } };

fetch('hello.wasm').then(response =>
    response.arrayBuffer()
).then(bytes =>
    // 创建实例，注入依赖
    WebAssembly.instantiate(bytes, importObj)
).then(result =>
    result.instance.exports.hello()
);
```


## 第3章 JavaScript中的WebAssembly对象
* WebAssembly.compile(buffer):<br>
    将.wasm编译为WebAssembly.Module<br>
    返回Promise<WebAssembly.Module>
    异常WebAssembly.CompileError

* WebAssembly.instantiate(buffer, importObject?):<br>
    importObject可以包含JavaScript方法、WebAssembly.Memory 和WebAssembly.Table<br>
    返回Promise<{ module: WebAssembly.Module, instance : WebAssembly.Instance }><br>
    异常WebAssembly.CompileError | WebAssembly.LinkError | WebAssembly.RuntimeError

* WebAssembly.instantiate(Module, importObject?):<br>
    上面的重载<br>
    返回Promise<WebAssembly.Instance><br>
    异常TypeError和上面存在的

* WebAssembly.validate(buffer):<br>
    校验WebAssembly二进制代码是否合法

* WebAssembly.compileStreaming(source),<br>
  WebAssembly.instantiateStreaming(source, importObject?):<br>
  基本同上，但有兼容性问题<br>
  soruce是流，一般是fetch的返回值

* var module = new WebAssembly.Module(buffer);
* var exports = WebAssembly.Module.exports(module); //获取导出信息
* var imports = WebAssembly.Module.imports(module); //获取导入信息


* var sections = WebAssembly.Module.customSections(module, secName); //获取自定义段信息

* var instance = new WebAssembly.Instance(module, importObject);
* WebAssembly.Instance.prototype.exports //获取导出内容

* var memory = new WebAssembly.Memory({ initial: number, maximum?: number });
* WebAssembly.Memory.prototype.buffer //获取对象对应的buffer对象
* WebAssembly.Memory.prototype.grow(number) //扩大内存对象的容量(会**复制**原有数据)

* var table = new WebAssembly.Table({element:'anyfunc', initial:number, maximum?: number }); //table对象现在只能用作函数指针表
* WebAssembly.Table.prototype.get(index) //获取索引上的函数
* WebAssembly.Table.prototype.set(index, value)
* WebAssembly.Table.prototype.length
* WebAssembly.Table.prototype.grow()

* 其他： 目前JavaScript的Number类型无法无损地表达64位整型数。<br>
这意味着虽然WebAssembly 支持i64 类型的运算，但是<br>与JavaScript 对接的导出函数不能使用i64 类型作为参数或返回值。