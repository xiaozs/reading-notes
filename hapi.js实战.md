# 《hapi.js实战》
## 第1章 hapi简介
```
吹了一波hapi，什么都没说。
```
                   

## 第2章 构建api
```
大部分API的设计和express差不多：
路由、中间件

有些特别的地方是，那个策略注册、注入的地方，
hapi预设了一些使用场景，所以可以在路由配置里面直接注入策略，
好处应该和依赖注入带来的好处是一样的。
```


## 第3章 构建网站

```
貌似静态服务本来就包含在路由里面，所以直接配置就行。

需要插件来支持模板功能，模板的api也需要全局配置
```


## 第4章 深入理解路由和handler

```
介绍了hapi路由的路径配置方式，和其优先级，
对于冲突路由会直接报错，所以不会像express的路由一样对绑定顺序有依赖。
我觉得这一点很好。
```
局部片段参数：
/nature/flowers/orchids/{images}.{ext}

多重参数：
/images/{category*3}/{image}.{ext}

可选参数：
/team/{name?}

通配符参数：
/{path*}

```
基本上和正则的符号意义差不多，下面是路由优先级：
```

||片段类型|例子|
|---|---|---|
|最确切|字面量片段|/something|
||混合片段|/some{thing}|
|最不确切|参数片段|/{something} <br> /{something?}|
||通配符判断|/{something*}|

```
路由的回调handler,可以完全通过配置来使用，
但是需要预先用server.handler方法进行注册，
这个也是依赖注入
```

```
介绍了服务器方法server.method。
```

```
路由先决条件，这个东西其实express也能做，
因为express的use能绑定多个handler，他们会一个接一个执行，

唯一不同的一点是，先决条件可以通过配置来用，
需要预先用server.method方法进行注册，
还是依赖注入。
```

## 第5章 理解请求和响应

```
hapi有一套生命周期：
```
|接收并解析请求|身份验证请求|验证请求|处理请求|验证响应|发送响应|
|---|---|---|---|---|---|
|创建request对象<br>查找匹配的路由<br>处理JSONP<br>解析Cookie|身份验证<br>读取或解析payload<br>身份验证payload|验证路径参数<br>验证查询参数<br>验证payload|运行先决条件<br>执行handler|验证响应|发送响应<br>等待tails发送成功|

```
可以用server.ext(event, method, [options])
在生命周期钩子上做扩展。
```

## 第6章 使用Joi验证
```
Joi是一个验证库，
只举一些有特色的地方：
```
* 存在类型转换，numberic字符串转数字，buffer转string。有开关设置进不进行转换。
* abortEarly开关，是否遇到第一个错误就停止验证。


## 第7章 使用插件构建模块化应用

```
插件其实就是一个函数，
第一个参数是server，也就是说可以用上所有的server的方法。

推荐把所有逻辑都写在插件里面？
这个问题要怎么解决呢？
1. 插件冲突
2. 插件依赖
```

```
插件间通讯：

server.expose("methodName", () => { return val; }) 在插件内注册一个函数，
server.plugins["plugins-name"].methodName() 来调用

server.decorate("server", "propName", obj);
server.propName //obj;
```