# 《PWA开发实战》
## 第1章 渐进式Web应用介绍
优势：
1. 无连接状态下的可用性
2. 加载速度快
3. 推送通知
4. 主屏幕快捷方式

## 第2章 你的第一个service worker
大部分和《PWA实战》里面重复<br>
但有里面有一个scope域的设置问题：
```javascript
// 这两条命令将具有完全相同的作用域：
navigator.serviceWorker.register("/sw.js");
navigator.serviceWorker.register("/sw.js", {scope: "/"});

// 这两条命令将注册两个不同的service worker
// 每个service worker各自控制了一个不同的目录：
navigator.serviceWorker.register("/sw-ginnos.js", {scope: "/Ginnos"});
navigator.serviceWorker.register("/sw-ralphs.js", {scope: "/Ralphs"});
```

## 第3章 CacheStorage API
这一章同上。

## 第4章 service worker生命周期和缓存管理
这一章对生命周期的讨论比较完备，推荐复习

生命周期: 
* installing: 有钩子函数install，在侦测到新service worker脚本的时候会被执行，<br>
这一步一般用来缓存应用脚本
* installed/waiting:
    * installed: installing成功完成了以后
    * waiting: 但浏览器中有使用旧版service worker的页面时候
* activating: 有钩子函数activate，一般用于清楚旧缓存
* redundant: service worker被废弃


## 第5章 拥抱离线优先
常用缓存模式：
* 仅缓存
* 缓存优先，网络作为回退方案
* 仅网络
* 网络优先，缓存作为回退方案
* 先缓存，后网络（先取缓存，同时发网络请求，这个比较复杂要修改源代码）
* 通用回退(缓存中找不到，并且网络不可用时，返回默认报文)

还有（这几个更像是上面的完善版本）：
* 按需缓存
* 缓存优先，网络作为回退方案，并频繁更新缓存
* 网络优先，缓存作为回退方案，并频繁更新缓存




## 第6章 使用IndexedDB在本地存储数据

```javascript
//一旦授权后，存储的任何内容将不会被设备自动删除。内容只能通过用户操作进行删除。
if (navigator.storage && navigator.storage.persist) {
    navigator.storage.persist().then(function(granted) {
        if (granted) {
            console.log("Data will not be deleted automatically");
        }
    });
}
```

常用IndexedDB库：
* PouchDB
* localForage
* Dexie.js
* IndexedDB Promised




## 第7章 使用后台同步保证离线功能
```javascript
//获取对象

//worker
self.registration
//浏览器
navigator.serviceWorker.ready.then(function(registration) {});
```


```javascript
//注册事件

//worker
self.registration.sync.register("send-messages");
//浏览器
navigator.serviceWorker.ready.then(function(registration) {
    registration.sync.register("send-messages");
});
```

sync事件触发时机：
* sync事件注册后立即发送
* 当用户状态从离线变成在线时
* 每隔几分钟，如果有尚未完成的注册时

```javascript
//注册事件,类似于trigger,
//但它会多次尝试，到了event.waitUntil()成功时
//尝试时机如上
registration.sync.register("send-messages");

//类似于on
self.addEventListener("sync", event => {
    if(event.tag === "send-messages") {
        event.waitUntil();
    }
});

//获取已注册sync事件列表
registration.sync.getTags()
self.registration.sync.getTags()

//最后机会，如果多次不行，注册会被取消
//判断是否最后机会
e.lastChance
```


## 第8章 使用postMessage()在service worker和页面之间通信
* 窗口向service worker通信
```javascript
navigator.serviceWorker.controller.postMessage(data)

self.addEventListener("message", function (event) {
    console.log("data", event.data);
    console.log("window id", event.source.id);
    console.log("window url", event.source.url);
    console.log("and is", event.source.focused ? "focused" : "not focused");
    console.log("and", event.source.visibilityState);
});
```
* service worker向所有打开的窗口通信
```javascript
navigator.serviceWorker.addEventListener("message", function (event) {
    console.log(event.data);
});

self.clients.matchAll().then(function(clients) {
    clients.forEach(function(client) {
        client.postMessage("Hi client: " + client.id);
    });
});
```
* service worker向特定窗口通信
```javascript
self.clients.get(event.source.id).then(function(client) {
    client.postMessage(data);
});
```
* 使用MessageChannel保持通信渠道打开
```javascript
//可以在窗口中创建一个新的 MessageChannel 对象，并通过postMessage 将其中的一个端口传递给service worker
var msgChan = new MessageChannel();
msgChan.port1.onmessage = function(msg) {
    console.log("Message received at port 1:", msg.data);
};
msgChan.port2.postMessage("Hi from port 2");
```
* 窗口间的通信<br>
组合上面的做法


## 第9章 可安装的Web应用：占领主屏先机
安装提示显示条件：
```
// 清单文件格式看《PWA实战》第5章
if (
    Web应用提供HTTPS &&
    Web应用注册了service worker &&
    应用拥有有效的清单文件，其中包含了所有必需的属性&&
    清单文件在用户访问的页面中有链接&&
    浏览器认为用户对于这款应用有持久的兴趣&&
    这款应用的安装横条在过去没有被显示和拒绝过
) { 
    显示Web应用安装横条
}
```