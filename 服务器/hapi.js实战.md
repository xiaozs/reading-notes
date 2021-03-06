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

```javascript
//插件间通讯：

server.expose("methodName", () => { return val; }) //在插件内注册一个函数，
server.plugins["plugins-name"].methodName() //来调用

server.decorate("server", "propName", obj);
server.propName //obj;
```


## 第8章 充分利用缓存
```javascript
// 设置缓存头
response.header(name, value, options)
server.route({
    config: {
        cache: {
            privacy: "private",
            expiresIn: 86400 * 1000
        }
    }
})
```
```javascript
//ETag
var hash = parseHash(data);
var response = replay(data);
response.etag(hash);
```


之后还对catbox（一个缓存相关的库）进行了介绍：
* 这个库应该是比较简单的，所作的事就是定义接口，主要的逻辑在各个策略包当中，可以切换策略包以无缝切换多种存储方式。
* 因为hapi里面内置了这个库，所以在配置过策略包后可以通过配置的方式来对某一个接口的缓存策略进行设置。
* 上面的这个方法也适用于服务器方法，在服务器方法进行注册时顺便配置一下就可以启用，所以是对原有代码来说是透明的。


上面的缓存方式一般是用的key-value对存储，为了避免冲突有这样的一些分区策略：
* **分区**：多个应用公用一个数据库的时候，每一个应用要对应一个分区，（分区这个说法是一个抽象，具体到实现的话，例如用的是mongodb，可以一个分区就是一个数据库）
* **段**：为了方便我们经常会用数据库的ID来做为key在缓存里面做存取，为了能样一个ID能取到不同的数据（key-value是一对一的），去要为分区分段


## 第9章 身份验证和安全
* 身份验证：鉴定或弄清除你是谁
* 授权：许可或弄清楚允许你做什么


```javascript
//身份验证

const validate = function(request, usernmae, password, callback){
    callback(err, isValid, { scope:["权限1"] })
}

server.auth.strategy("策略名称", "基础策略名称", { validateFunc: validate })

```
```javascript
// 授权
server.route({

    config: {
        auth: {
            strategy: "策略名称",
            scope: ["权限1"]
        }
    }

})

//try模式
server.route({

    config: {
        auth: {
            strategy: "策略名称",
            mode: "try"
        },
        handler: function(request, reply) {
            if(request.auth.isAuthenticated) { //身份验证是否通过
                
            }
        }
    }

})
```

```
那些第三方登录验证，也可以用这一套api来包装出自己的插件。
```

```
hapi中内置了对cors的支持，只要简单的配置，就可以在Server、connection、route三个粒度上设置对cors的支持。
```

避免csrf攻击的方法：
* csrf令牌：在生成页面的时候，在页面表单上加一个动态的隐藏字段。后台在接受到请求的时候要验证这个字段。
* 使用这种方法的时候某些页面不能设置cors，否则可以通过先下载页面取得csrf令牌


其余还有很多和http相关的安全知识，推荐复习。



## 第10章 使用Lab、Code和server.inject()进行测试

下面这两个没有什么好说的：
* Lab：测试执行器
* Code：断言库

server.inject：
* 这个api用于伪造请求（不通过http、不监听端口、）
 
之后还介绍了spy、stub这些测试相关的知识，（感觉比这本说得好懂《编写可测试的JavaScript 代码》）<br>
spy、stub可以使用sinon这个库<br>
还介绍了proxyquire这个库，<br>
（这个库的原理是劫持require函数，把require的模块替换成mock，<br>
和那些依赖注入是一个意思，但是由于是劫持系统函数，就搞得像是系统自带支持一样，特别的简单，透明）


## 第11章 投入生产环境及更多相关内容

* 日志记录：<br>
这里介绍的good这个库，api比较简单，只有一个函数<br>
第一个参数是一个数组，里面用string作为标签，<br>
第二个参数是日志文本。<br>
配置里面可以配置不同的标签对应不同输出方式。

* 文档：<br>
在hapi的的配置里面可以配置一些meta，<br>
给hapi注册lout这个插件，<br>
会在/docs这个路由下生成api文档，<br>
连接口的校验信息可以打出来（这一点特别好）

* 监控：<br>
介绍了statsD、oppsy两个包<br>
statsd：每次调用它的api就会产生一次记录，这些记录会生成一个和时间相关的图标。<br>
（调用它的时候有两个参数，第一个是图表id，另一个是数值，这个包应该很简单）<br>
oppsy：可以读取到hapi的运行状况

* 调试：<br>
建议去看一下那本《Node.js调试指南》

* SSL/TLS：<br>
SSL终止：中间层，在该层解密加密的SSL数据，并对未加密数据进行转发。
hapi中配置SSL很简单，配置：
```javascript
//可以同时监听443和80并将80重定向到443
server.connection({
    port: 443,
    tls: {
        key: "xxx",
        cert: "xxx"
    }
})

//这里又提了一遍hsts(第9章)，要找时间回去复习
```