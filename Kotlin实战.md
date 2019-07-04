# 《Kotlin实战》
## 第1章 Kotlin：定义和目的
介绍了一些基本语法、特性
```shell
#编译
kotlinc <source file or directory> -include-runtime -d <jar name>
#运行
java -jar <jar name>
```
## 第2章 Kotlin基础
* 函数
    * 函数表达式：```fun max(a: Int, b: Int) = if(a > b) a else b```
* 变量
    * 可变变量（var）
    * 不可变变量（val）
* 字符串模板
* 类和属性
    * 值对象：```class Person(val name:string)```，其属性默认public
    * 自定义访问器
        ```kotlin
        class Rectangle(val height: Int, val Width: Int) {
            val isSquare: Boolean
            get() {
                return height === width;
            }
        }
        ```
* 目录和包：没有对磁盘上源文件的布局强加任何限制
* 枚举和“when”
    * 枚举类
    ```kotlin
    enum class Color {
        RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
    }
    ```
    * 带属性的枚举类
    ```kotlin
    enum class Color (
        val r: Int, val g:Int, val b:Int
    ) {
        RED(255, 0, 0),
        ORANGE(255, 165, 0),
        YELLOW(255, 255, 0),
        GREEN(0, 255, 0),
        BLUE(0, 0, 255),
        INDIGO(75, 0, 130),
        VIOLET(238, 130, 238);

        fun rgb() = (r * 256 + g) * 256 + b
    }
    ```
    * 使用“when”处理枚举类
    ```kotlin
    fun getMnemonic(color: Color) = 
        when (color) {
            Color.RED -> "Richard"
            Color.ORANGE -> "Of"
            Color.YELLOW -> "York"
            Color.GREEN -> "Gave"
            Color.BLUE -> "Battle"
            Color.INDIGO -> "In"
            Color.VIOLET -> "Vain"
        }
    fun getWarmth(color: Color) = 
        when (color) {
            Color.RED, Color.ORANGE, Color.YELLOW  -> "warm"
            Color.GREEN -> "neutral"
            Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
        }
    fun mix(c1: Color, c2: Color) = 
        when (setOf(c1, c2)) {
            setOf(RED, YELLOW) -> ORANGE
            setOf(YELLOW, BLUE) -> GREEN
            setOf(BLUE, VIOLET) -> INDIGO
            else -> throw Exception("Dirty color")
        }
    ```
* 合并类型检查和转换：
```kotlin
if(e is XXX) {
    //这里的e的类型自动是XXX
}
```
* 迭代带步长的区间
```kotlin
for(i in 100 downTo 1 step 2 ) {

}

// size是变量
for(i in 0 until size) {

}
// 等同于
for(i in 0.. size-1) {

}
```
* 使用in检查集合和区间的成员：
```kotlin
c in 'a'..'z'
c !in 'a'..'z'
```

