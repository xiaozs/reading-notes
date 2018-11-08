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

|类|描述|
|---|---|
|XAttribute|表示XML中的一个属性|
|XCData|表示一个CDATA文本节点|
|XComment|表示一个XML注释|
|XContainer|一个抽象基类，表示具有子节点的节点|
|XDeclaration|表示一个XML声明|
|XDocument|表示一个XML文档，该类派生自XContainer类|
|XDocumentType|表示一个XML DTD（文档类型定义）|
|XElement|表示一个XML元素，它是XContainer的派生类|
|XName|表示一个XML元素或属性的名称|
|XNamespace|表示一个XML名称空间|
|XNode|该抽象类表示XML元素树的节点|
|XNodeDocumentOrderComparer|根据节点在XML文档累内的顺序对它们进行比较|
|XNodeEqualityComparer|根据节点的值对他们进行比较|
|XObject|表示XNodes和XAttributes的抽象类|
|XObjectChange|XObject时间发生时的事件类型|
|XObjectChangeEventArgs|为Changing和Changed事件的方法|
|XProcessingInstruction|表示一个XML处理指令|
|XText|表示一个XML文本节点|


这一组api对于树形结构的增删查改有一定的参考性：<br>
(这里只列出了一部分)

|XElement类方法|描述|
|---|---|
|AddAnnotaion|为给定的XElement添加注释。一般来说，这种方法为响应的XObject（树的当前节点或属性）的注释添加对象|
|AncestorsAndSelf|返回一个元素集合，此集合包含当前元素及其所有前驱节点。前驱节点引以为当前节点的（可能多个）双亲节点（意思是，当前节点的双亲节点，以及双亲节点的双亲节点，由此形成的节点链）|
|Attribute|返回单个属性，此属性是同给定的XName相匹配的当前XElement的XAttribute，也就是说，这个方法返回他找到的具有特定名称的给定元素的第一个属性|
|Attributes|返回当前元素的所有属性（一个集合）：也可以指定一个名称，此时将返回具有指定名称的元素的所有属性|
|CreateReader|为当前节点创建一个XmlReader（一个快速的、只能向前遍历的XML文档副本）|
|CreateWriter|为XML文档创建一个XmlWriter，它提供修改XML文档的功能，例如：加入节点或属性。XmlWriter是一种快速的、只能前向的机制，用于为内存中的XML文档创建文件|
|DescendantNodes|返回整个文档或当前节点/元素的所有后继节点的集合|
|DescendantNodesAndSelf|返回与DescendantNodes方法相同的集合，但在集合中也同时包括当前节点|
|DescendantsAndSelf|返回包含当前元素及其所有后继元素的集合。也可以指定一个名称，只返回集合中与指定名称相匹配的元素|
|Element|返回已排序的XML文档中通知定的元素名称相匹配的第一个元素|
|IsAfter|返回一个指示当前节点是否出现在指定节点之后的布尔值|
|IsBefore|返回一个指示当前节点是否出现在指定节点之前的布尔值|
|Load|提供多种机制从外部数据源创建新的XElements。数据源可以包括TextReader、String或XmlReader（每种数据源都带有一个额外的选项以保留空白）|
|Nodes|返回当前元素或文档的所有子节点的集合|
|NodesAfterSelf|返回一个位于当前节点之后（即后续节点）的有序节点的集合|
|NodesBeforeSelf|返回一个位于当前节点之前的有序节点的集合|
|Parse|从一个包含XML的字符串加载一个XML文档。可以选择性地保留空白|
|Remove|从双亲节点中移除当前节点|
|RemoveAll|删除当前元素的所有节点和属性|
|RemoveAttributes|删除当前元素的所有属性|
|RemoveNodes|删除XML文档或当前元素的所有节点|
|RplaceAll|用指定内容替换当前元素的所有子节点和属性|
|RplaceAttributes|用指定内容替换当前元素的所有属性|
|Save|将当前元素的XML树序列化到几个目的位置之一，如文件、XmlTextWriter、XmlWriter或TextWriter|
|SetAttributeValue|设定当前属性的值|
|SetElementVaule|设定子元素的值|
|SetValue|设定当前元素的值|
|WriterTo|将当前元素写入到一个XmlWriter|


## 第6章 LINQ to XML 编程
同上章

## 第7章 LINQ to XML 和其它LINQ数据模型
说了几个LINQ to XML配合其它LINQ api做的demo

## 第8章 LINQ to XML 编程高级主题
只有**轴**和**事件**两个主题有一定参考价值

流式处理XML文档也有点意思，但是我觉得通过事件的方式来进行流式处理，可能会更好，不会出现demo里面的深嵌套和各种if/else



## 第9章 LINQ to XML 和Visual Basic .NET
介绍了vb.net操作xml的方法，<br>
生成的话用的是类似于模板的语法，<br>
查询的话是用的扩展索引器（语法有点特殊，不懂），<br>
其余的地方是大差不差。



## 第10章 LINQ to SQL 概述
```CSharp
[Table(Name = "Person.Contact")]
public class Contact {
    [Column(DBType = "nvarchar(8)")]
    public string Title;
}

DataContext context = new DataContext("连接字符串");
Table<Contact> contact = context.GetTable<Contact>();
```


|LINQ to SQL 对象|关系对象|
|---|---|
|DataContext|数据库|
|实体类|表|
|类成员|列|
|关联|外键关系|


```CSharp
//用于指定数据库名称
[Database(Name = "dbName")]
public class Contact {

}
```

|Column 特性|描述|默认值|
|---|---|---|
|Name|数据表中列的名称||
|DbType|数据库列的数据库类型||
|Storage|实体类的存储字段/变量||
|IsPrimaryKey|指示所关联的列是否是相应表的主键|false|
|IsDbGenerated|指示所关联的列是否自动生成它的值|false|
|CanBeNull|指示所关联的列能否包含空值|true|
|AutoSync|通知运行时在执行INSERT或者UPDATE操作之后获得列值。<br>Always、Never、OnUpdate、OnInsert|Never|
|Expression|定义一个被计算的数据库列||
|IsVersion|指示所关联的列是时间戳还是版本号|false|
|UpdateCheck|指示LINQ to SQL如何处理并发冲突<br>Always、Never、WhenChanged||
|IsDiscriminator|指示该列是否包含LINQ to SQL的继承层次的区别标识||


|Association 属性|描述|
|---|---|
|Name|关联的名称|
|Storage|指示存储字段/变量|
|IsUnique|指示FK是否唯一约束|
|IsForeignKey|指示连接/约束是否是一个外键|
|ThisKey|指示实体类的成员表示关联中此方的键值|
|OtherKey|指示目标实体类的一个或多个成员作为关联中另一方的键值|




```CSharp
//用于指定数据库名称
[Association(
    Name =  "FK_Employee_Contact_ContactID",
    Storage = "_Employee",
    ThisKey = "ContactID",
    IsForeignKey = true
)]
public Employee Emp {
    get { return this._Employee.Entity; }
    set { this._Employee.Entity = value; }
}
private EntityRef<Employee> _Employee;
```



## 第11章 LINQ to SQL 查询
增：
```CSharp
public class AdventureWorks: DataContext {
    public AdventureWorks(string connection): base(connection) {}
    public Table<Contact> Contact;
}
try {
    AdventureWorks db = new AdventureWorks("连接字符串");
    Contact con = new Contact();
    con.Title = "Geek";
    db.Contact.Add(con);
    db.SubmitChanges();
} catch(Exception ex) {

}
```
删：
```CSharp
var con = db.Contact.Single(cb);
db.Contact.Remove(con);
db.SubmitChanges();
```
改：
```CSharp
var con = db.Contact.Single(cb);
con.title = "new";
db.SubmitChanges();
```



## 第12章 高级查询概念

```CSharp
[Table(Name = "Person.Contact")]
public class Contact {
    //一对多关系
    private EntitySet<Employee> _employees;
    [Association(Storage = "_employees", otherKey = "ContactID")]
    pulic EntitySet<Employee> Emps {
        get { return this._employees; }
        set { this._employees.Assign(value); }
    }
}


var conQuery = 
    from con in db.Contact
    from emp in con.Emps
    where con.FirstName == "Scott"
    select new { con.FirstName, em.ManagerID };
```
```CSharp
//默认情况下是，远程执行

//本地执行方法如下：
Contact con = db.Contact.Single(cb);
con.Employee.load();
```



## 第13章 实体类
乐观并发：
* 用用程序试图将变更写回数据库。
* 自从请求数据后，这个被请求的数据在数据库中已经发生了变更。

|UpdateCheck 值|描述|
|---|---|
|Always|一直使用这个成员检测冲突(默认)|
|Never|不使用这个成员来确定冲突|
|WhenChanged|当这个成员的值已经被应用程序改动时才使用该成员检测冲突|

```CSharp
//尝试所有的数据库更新，收集所有并发冲突并在更改完成后将它们返回。
ConfilctMode.ContinueOnConflict
//当检测到第一个并发冲突时立即终止更新操作
ConfilctMode.FailOnFirstConflict
db.SubmitChanges(ConfilctMode.ContinueOnConflict)

//冲突会引发异常
ChangeConflictException
```

```CSharp
try {
    db.SubmitChanges(ConfilctMode.ContinueOnConflict)
} catch(ChangeConflictException ex) {
    foreach(var oc db.ChangeConflicts) {
        //保留对象中已经更改的当前值，但将其他值更新为数据库中的值。
        RefreshMode.KeepChanges
        //使用从数据库中检索的值替换当前对象的值
        RefreshMode.KeepCurrentValues
        //使用数据库中的值覆盖所有当前对象的值
        RefreshMode.OverwriteCurrentValues
        oc.Resolve(RefreshMode.KeepCurrentValues);
    }
}
```

使用事务：
```CSharp
//隐式
var db = new AdventureWorks("连接字符串");
try {
    using (var ts = new TransactionScope()){
        var con = new Contact();
        db.Contacts.Add(con);
        db.SubmitChanges();
    }
} catch(Exception ex) {

}
```
```CSharp
//显式
var db = new AdventureWorks("连接字符串");
db.Connection.Open();
db.Transaction = db.Connection.BefinTransaction();
var con = new Contact();
db.Contacts.Add(con);
db.SubmitChanges();
db.Transaction.Commit();
db.Transaction.Dispose();
db.Connecction.Close();
```
