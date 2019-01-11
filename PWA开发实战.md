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