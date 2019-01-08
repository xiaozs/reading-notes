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

---

* var module = new WebAssembly.Module(buffer);
* var exports = WebAssembly.Module.exports(module); //获取导出信息
* var imports = WebAssembly.Module.imports(module); //获取导入信息

---

* var sections = WebAssembly.Module.customSections(module, secName); //获取自定义段信息

---

* var instance = new WebAssembly.Instance(module, importObject);
* WebAssembly.Instance.prototype.exports //获取导出内容

---

* var memory = new WebAssembly.Memory({ initial: number, maximum?: number });
* WebAssembly.Memory.prototype.buffer //获取对象对应的buffer对象
* WebAssembly.Memory.prototype.grow(number) //扩大内存对象的容量(会**复制**原有数据)

---

* var table = new WebAssembly.Table({element:'anyfunc', initial:number, maximum?: number }); //table对象现在只能用作函数指针表
* WebAssembly.Table.prototype.get(index) //获取索引上的函数
* WebAssembly.Table.prototype.set(index, value)
* WebAssembly.Table.prototype.length
* WebAssembly.Table.prototype.grow()

---

* 其他： 目前JavaScript的Number类型无法无损地表达64位整型数。<br>
这意味着虽然WebAssembly 支持i64 类型的运算，但是<br>与JavaScript 对接的导出函数不能使用i64 类型作为参数或返回值。

## 第4章 WebAssembly汇编语言

S-表达式：用于描述树状结构的一种文本格式

---

|节点|类型|
|---|---|
|module|WebAssembly 模块根结点，即Module|
|memory|Memory|
|data|Memory 初始值|
|table|Table|
|elem|Table 元素初始值|
|import|导入对象|
|export|导出对象|
|type|函数签名|
|global|全局变量|
|func|函数|
|param|函数参数|
|result|函数返回值|
|local|局部变量|
|start|开始函数|
|mut|可变类型（全局变量默认不可变）|
|offset|内存初始化偏移|

---

* 数据类型
    1. i32
    2. i64
    3. f32
    4. f64
    
---

* 函数定义: (func <函数别名> <函数签名> <局部变量表> <函数体>)  //4部分都可选
* 函数签名: (param <变量别名> i32) (param <变量别名> f32) (result <变量别名> f64)
* 局部变量表: (local i32) (local f32)
* 函数别名: 用于替代 func[0] func[1] 这些

---

* 全局变量: (global <别名> <类型> <初值>)

---

* get_local: 通过用索引或变量别名，获取param和local的值
* set_local: 反之

---

* i32.const n：在栈上压入值为n 的32 位整型数。
* i32.add：从栈中取出2 个32 位整型数，计算它们的和并将结果压入栈。
* i32.eq：从栈中取出2 个32 位整型数，比较它们是否相等，相等的话在栈中压入1，否则压入0。

---

* 直接调用: call <函数索引 | 函数别名>
* 间接调用: 
```wat
//调用参数压栈
//被调用的函数的table index压栈
call_indirect (type <type索引 | type别名>)
```

---

* 内存声明: (memory <大小>)
* 初始化: (data (offset i32.const 0) "hello")  //可以多次调用(小端)
* 读取内存: 
```wat
;;读取内存12出的整数到栈上

i32.const 12
i32.load
```
或 
```wat
;;读取内存12出的整数到栈上
;;align=4 是地址对齐标签

i32.const 4
i32.load offset=8 align=4
```
其他
```wat
;;i32.load<bite数>_<有无符号>

i32.load8_s
i32.load8_u
i32.load16_s
i32.load16_u
i64.load8_s
i64.load8_u
i64.load16_s
i64.load16_u
i64.load32_s
i64.load32_u
```

* 写入内存:
```wat
;;地址12处写入42
i32.const 12 ;;address
i32.const 42 ;;value
i32.store

;;其他
i32.const 4  ;;address
i32.const 42 ;;value
i32.store offset=8 align=4
```
其他
```wat
i32.store8
i32.store16
i64.store8
i64.store16
i64.store32
```
* 获取内存容量:
```wat
memory.size
```
* 内存扩容:
```wat
;;入栈扩容大小
memory.grow
```

---

删除栈元素: drop

---

* 啥都不干: nop
* 抛出异常: unreachable
* block 指令块:
```wat
block <块别名>
    ;;指令块拥有自己独立的栈帧
    ;;指令块可以有返回值
end
```
或
```wat
block <块别名> (result i32)
    i32.const 13
end
```
* if 指令块:
```wat
if <块别名>
    ;;分支1
else
    ;;分支2
end
```
或
```wat
if <块别名> (result <类型>)
    ;;分支1
else
    ;;分支2
end
```
* loop 指令块:
```wat
loop <块别名>
    ;;只会执行1次，要和br配合使用
end
```
* 跳转:
```wat
;;跳转到depth深度的块的开始
;;当前块的深度为0
;;套着当前块的块的深度为1
;;以此类推
br <depth>

;;或者
br <块别名>
```
或
```wat
;;会先从栈上弹出一个i32类型的值，如果该值不为0，则执行br
br_if <块别名 | depth>
```
或
```wat
;;L[n]是一个长度为n 的label 索引数组
;;先从栈上弹出一个i32类型的值m，如果m 小于n，则执行br L[m]，否则执行br L_Default
br_table L[n] L_Default

;;类似这样 br_table 2 1 0
```

* return: 跳出函数

---

* 导出对象:
```wat
(module
    (func (export "wasm_func") (result i32)
        i32.const 42
    )
    ;;声明同时导出
    (memory (export "wasm_mem") 1)
    (table (export "wasm_table") 2 anyfunc)
    (global (export "wasm_global_pi") f32 (f32.const 3.14159))
)
```
或
```wat
(module
    (func (result i32)
        i32.const 42
    )
    (memory 1)
    (table $t 2 anyfunc)
    (global $g0 f32 (f32.const 3.14159))

    ;;先声明，后导出
    (export "wasm_func" (func 0))
    (export "wasm_mem" (memory 0))
    (export "wasm_table" (table $t))
    (export "wasm_global" (global $g0))
)
```

* 导入对象:
```wat
(module
    (import "js" "memory" (memory 1)) ;;import Memory
    (import "js" "table" (table 1 anyfunc)) ;;import Table
    (import "js" "print_i32" (func $js_print_i32 (param i32))) ;;import Fucntion
    (import "js" "global_pi" (global $pi f32)) ;;import Global
)
```

---

* start函数：
```wat
(module
    ;;实例化后能够立即执行一些启动操作
    ;;启动函数不能包含参数，不能有返回值
    (start $print_pi)
    (import "js" "print_f32" (func $js_print_f32 (param f32)))
    (func $print_pi
        f32.const 3.14
        call $js_print_f32
    )
)
```

---

* 指令折叠:
```
i32.const 13
get_local $x
i32.add

;;等价于

(i32.add (i32.const 13) (get_local $x))

```