# 《LINQ高级编程》
## 第1章 LINQ项目
介绍了使用LINQ的理由和使用LINQ的一些简单demo

## 第2章 Visual Studio 2008 简介
介绍了vs的一些历史，没用。

LINQ特性：
* 查询表达式：LINQ的那个dsl，除非会编译否则没有参考性。
* 隐式的类型化变量：var 关键字，js等动态语言自带。
* 匿名类型：js自带
* 对象和集合初始化器：设置对象的属性、设置集合的元素的特殊语法，只要该类型实现特定接口就能使用，应该算是一种元编程
* 扩展方法：一种化函数为方法的语法糖，js上模拟不来，只能使用特殊类型配合链式api了。
* Lambda表达式：箭头函数

## 第3章 LINQ查询
* 获取数据源
* 创建查询
* 执行查询
    1. 延迟执行
    2. 立即执行

* 常见的查询术语
    1. LINQ Provider：都实现了IQueryable接口
    2. 表达式树：IQueryable的方法会生成表达式树给Provider使用。

## 第4章 LINQ标准查询操作符
* 投影操作符：
    1. select
    2. selectMany

* 限制操作符：
    1. where

* 排序操作符：
    1. orderBy
    2. orderByDescending
    3. thenBy
    4. thenByDescending
    5. reverse

* 联接操作符：
    1. join
    2. groupJoin

* 分组操作符：
    1. groupBy

* 串联操作符：
    1. concat

* 聚合操作符：
    1. aggregate
    2. average
    3. count
    4. longCount
    5. max
    6. min
    7. sum

* 集合操作符：
    1. distinct
    2. union
    3. intersect
    4. except

* 生成操作符：
    1. empty
    2. range
    3. repeat

* 转换操作符：
    1. asEnumerable
    2. cast
    3. ofType
    4. toArray
    5. toDictionary
    6. toList
    7. toLookup

* 元素操作符：
    1. defaultIfEmpty
    2. elementAt
    3. elementAtOrDefault
    4. first
    5. firstOrDefault
    6. last
    7. lastOrDefault
    8. single
    9. singleOrDefault

* 相等操作符：
    1. sequenceEqual

* 量词操作符：
    1. all
    2. any
    3. contains

* 分割操作符：
    1. skip
    2. skipWhile
    3. take
    4. takeWhile
    

## 第5章 理解LINQ to XML
