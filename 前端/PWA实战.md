# 《PWA实战 面向下一代的Progressive Web APP》
# 第1章 理解PWA
```
虽然我对PWA是个啥不怎么感兴趣。
但是这一章里面还是提到了一下技术的小细节：
```
Service Worker:
* 运行在自己的脚本上下文上。
* 不绑定到具体网页
* 无法修改网页中的元素，因为无法访问DOM
* **只能使用HTTPS** (这个没想到)
* 在注册后第一次刷新或跳转其他页面的时候才起作用

代码：
```html
<script>
    //属性探测，是否支持serviceWorker
    if("serviceWorker" in navigator) {
        //注册，注册文件为sw.js
        navigator.serviceWorker.register("/sw.js").then(() => {
            //注册成功
        }).catch(() => {
            //注册失败
        })
    }
</script>
```
```javascript
//sw.js

//绑定HTTP请求事件
self.addEventListener("fetch",(event) => {
    //请求的url
    event.request.url

    //请求拦截并调包
    event.respondWidth(fetch("替换物的url"))
})
```

# 第2章 构建PWA的第一步

```
简单介绍了一些PWA相关的知识，
例如：
缓存
自定义错误页面
manifest设置应用信息

都只是简单介绍，应该接下来会有展开
```

# 第3章 缓存
```js
//sw.js

//install事件，在sw.js第一次注册的时候触发
self.addEventListener("install", event => {
    event.waitUntil(                            //这个方法以一个promise为参数,用来探测是否安装成功的
                                                //（这个api究竟是谁设计的，直接回调要求返回一个promise不好么）
                                                //（可能和skipWaiting这些api有关系）
        caches.open(cacheName)                  //打开一个特定名称的缓存，这个方法返回一个promise
            .then(cache => cache.addAll([       //添加缓存
                "/js/script.js",
                "/images/hello.png"
            ]))
    )
})

//HTTP请求事件
self.addEventListener("fetch", event => {
    event.respondWidth(
        caches.match(event.request)         //在缓存中通过url查找匹配(这里又不用cacheName，搞不懂)
            .then(response => {
                if(response){
                    return response;        //缓存命中，返回缓存内容
                } 
                return fetch(event.request) //往常一样获取资源
            })
    )
})
```
```js

//改进：请求并缓存
self.addEventListener("fetch", event => {
    event.respondWidth(
        caches.match(event.request, { ignoreSearch: true })             //忽略url的查询参数 
            .then(response => {
                if(response){
                    return response; 
                } 
                //以上一样

                var requestToCache = event.request.clone();             //复制请求，请求是一个流只能使用一次

                return fetch(requestToCache).then(response => {     
                    if(!response || response.status !== 200) {      
                        return response;                                //返回错误信息
                    }

                    var responseToCache = response.clone();
                    caches.open(cacheName)
                        .then(cache => {
                            cache.put(requestToCache, responseToCache)  //添加缓存
                        })

                    return response;
                })
            })
    )
})
```

```
Service Worker文件会在每次访问网站时在后台对比自身，如果不同，会重新执行器生命周期
其他文件 建议使用缓存破坏(文件名加hash)

ignoreSearch    //忽略请求参数
ignoreMethod    //忽略请求方法
ignoreVary      //忽略vary响应头
```


```
用importScripts方法加载库。

workbox提供的一些缓存策略:
```
* 缓存优先
* 只通过缓存获取
* 网络优先
* 只通过网络获取
* 从缓存或网络中找到最快的响应

## 第4章 拦截网络请求
```
介绍了一波fetch的api，其中的Respone可以自定义HTTP响应
```

```javascript
//如前述，service worker在注册后第一次刷新或跳转其他页面的时候才起作用，
//以下是马上起效的方法：
self.addEventListener("install", (event) => {
    event.waitUntil(self.skipWaiting()); //触发activate事件
})
//还可以与self.clients.claim一起用，确保底层service worker立即生效
self.addEventListener("activate", (event) => {
    event.waitUntil(self.clients.claim());
})
```

## 第5章 观感

```json
//manifest.json
{
    "name": "提示用户安装时的文本",
    "short_name": "安装后的文本",
    "start_url": "打开应用的第一个页面url", //可以在上面加上查询字符串，以追踪访问来源
    "display": "fullscreen",    //全屏
    "display": "standalone",    //没url栏，有状态栏
    "display": "minimal-ui",    //最小UI元素集合（后退、前进、重载按钮等）
    "display": "browser",       //浏览器方式打开
    "theme_color": "地址栏颜色",
    "background_color": "背景色",
    "icons": [{
        "src": "应用图标",
        "sizes": "192x192",     //图标大小
        "type": "image/png"
    }],
    "description": "应用的描述",
    "orientation": "屏幕方向",   //landscape portrait auto 3种
    "scope": "本文件只对命中的url生效",
    "dir": "文本方向",            //ltr rtl auto
    "lang": "语言",
    "prefer_related_applications": true,   //配合related_applications使用，推荐原生app
    "related_applications": [{
        "platform": "play",
        "url": "https://play.google.com/store/apps/details?id=com.goth.app",
        "id": "com.goth.app"
    }, {
        "platform": "itunes",
        "url": "https://itunes.apple.com/app/gotham-imperial/id1234"
    }]
}
```
```html
<!--引入方式-->
<link rel="manifest" href="/manifest.json" />
```
```javascript
//阻止安装弹框
window.addEventListener("beforeinstallprompt", (e) => {
    e.preventDefault();
    return false;
})
```


```javascript
//判断用户选择
window.addEventListener("beforeinstallprompt", (e) => {
    event.userChoice.then((result) => {
        result.outcome === "dismissed"
    })
})
```


```javascript
//推迟提示
var savedPrompt;
window.addEventListener("beforeinstallprompt", (e) => {
    e.preventDefault();
    savedPrompt = e;
    return false;
})

btn.addEventListener("click", () => {
    if(savedPrompt) {
        savedPrompt.prompt();   //弹出提示
    }
})

```

## 第6章 推送通知
```javascript
//前端
var key;
var authSecret;
var endpoint;
var vapidPublicKey = "" //公钥，前后都要

//将VAPID密钥从base64字符串转换成Uint8数组，因为这是VAPID规范要求的
function urlBase64ToUint8Array(base64String){
    const padding = "=".repeat((4 - base64String.length % 4) % 4);
    const base64 = (base64String + padding)
        .replace(/\-/g, "+")
        .replace(/_/g, "/")

    const rawData = window.atob(base64);
    const outputArray = new Uint8Array(rawData.length);
    for(let i = 0; i < rawData.length; i++){
        outputArray[i] = rawData.charCodeAt(i);
    }
    return outputArray;
}

if ("serviceWorker" in navigator) {
    navigator.serviceWorker.register("sw.js").then(registration => {
        return registration.pushManager.getSubscription();
            .then(subscription => {
                if(subscription) {
                    //如果已经订阅
                    return;
                }
                return registration.pushManager.subscribe({ //这里弹出订阅选择框
                    userVisibleOnly: true,
                    applicationServerKey: urlBase64ToUint8Array(vapidPublicKey)
                }).then(subscription => {
                    //从订阅对象中获取密钥和authSecret
                    var rawKey = subscription.getKey ? subscription.getKey("p256dh") : "";
                    key = rawKey ? btoa(String.fromCharCode.apply(null, new Uint8Array(rawKey))) : "";
                    var rawAuthSecret = subscription.getKey ? subscription.getKey ? subscription.getKey("auth") : ""; 
                    authSecret = rawAuthSecret ? btoa(String.fromCharCode.apply(null, new Uint8Array(rawAuthSecret))) : "";

                    endpoint = subscription.endpoint;

                    //注册推送
                    return fetch("./register", {
                        method: "post",
                        headers: new Headers({
                            "content-type": "application/json"
                        }),
                        body: JSON.stringify({
                            endpoint: subscription.endpoint,
                            key: key,
                            authSecret: authSecret
                        })
                    })
                })
            })
    }).catch(err => {
        //注册失败
    })
}
```

```javascript
//后端
//vapid是一个协议，用于应用服务器和推送服务之间的握手
const webpush = require("web-push");

webpush.setVapidDetails(xxx,xxx,xxx);//设置vapid详情

//订阅用户相关的信息
const pushSubscription = {
    endpoint,
    keys:{
        auth: authSecret,
        p256dh: key
    }
}

webpush.sedNotification(pushSubscription,
    JSON.stringify({    //貌似第2个参数才是真正推送内容，是个字符串
        msg: "推送内容",
        url: "发送消息的服务器url",
        icon: iconUrl
    })
)

```

```javascript
//前端
//接收通知
self.addEventListener("push", event => {
    var payload = event.data ? JSON.parse(event.data.text()) : "no payload";
    event.waitUntil(
        self.registration.showNotification(title, { //弹出通知
            body: payload.msg,
            url: payload.url,
            icon: payload.icon,

            actions: [  //出现在通知中的操作
                { action: "voteup", title: "Vote up" },
                { action: "votedown", title: "Vote Down" }
            ],
            vibrate: [300, 100, 400]    //震动300ms，暂停100ms，震动400ms
        })
    )
})
//互动（跳转通知）
self.addEventListener("notificationclick", event => {
    event.notification.close();     //关闭通知

    event.waitUntil(
        //检查当前窗口是否已经打开
        clients.matchAll({
            type: "window"
        }).then(clientList => {
            for(var i = 0; i < clientList.length; i++) {
                var client = clientList[i];
                if (client.url == "/" && "focus" in client) {
                    return client.focus();
                }
            }
            if (clients.openWindow) {
                return clients.openWindow("页面url")
            }
        })
    )
})
// 带操作的
self.addEventListener("notificationclick", event => {
    event.notification.close();     //关闭通知
    
    if(event.action === "voteup") {
        clients.openWindow("voteup页面url")
    } else {
        clients.openWindow("votedown页面url")
    }
})
```

```javascript
//取消订阅
if ("serviceWorker" in navigator) {
    navigator.serviceWorker.ready.then(registration => {
        registration.pushManager.getSubscription().then(subscription => {
            if(!subscription) return;
            subscription.unsubscribe();
        })
    })
}
```

## 第7章 离线浏览

```javascript
//网络优先的一个例子，其他的模式在第3章有述
self.addEventListener("fetch", event => {
    let url = event.request.url;
    event.respondWith(
        fetch(url).then(response => {
            if(!response || response.status !== 200){
                return response;
            }

            var responseToCache = response.clone();
            caches.open(cacheName).then(cache => {
                cache.put(event.request, responseToCache);
            })
            return response;
        }).catch(error => {     //若果fetch失败，则捕获此错误
            return caches.match(url);   //返回缓存内容
        })
    )
})
```

```javascript
//上线、离线的两个事件
window.addEventListener("online",() => {});
window.addEventListener("offline",() => {});
```

## 第8章 构建更富弹性的应用

* lie-fi：不是离线但是下载速度极慢的情况。
* 单点故障（SPOF）：部分第三方脚本引起的阻塞，导致页面空白。（可用WebPageTest测试）


```javascript
// 其实就是给请求设置超时
// 一旦超时就用缓存替代
self.addEventListener("fetch", event => {
    event.respondWith(Promise.race([timeout(3000), fetch(event.request.url)]));
})
```

## 第9章 保持数据同步

```javascript
navigator.servieWorker.register("sw.js")
    .then(registration => navigator.serviceWorker.ready)    //ready是一个promise, 这里相当于await ready
    .then(registration => {
        btn.onClick(() => {
            //相当于trigger
            registration.sync.register("contact-email").then(() => {    //注册同步
                //保存数据
            })
        })
    })

// sw.js
//类似于on
self.addEventListenr("sync", event => {
    if(event.tag === "contact-email"){
        event.waitUntil(
            //获取保存的数据
            //发送数据给后台
            //删除保存的内容
        )
    }
})
```

```javascript
//定期同步，不稳定的api
navigator.serviceWorker.ready.then(registration => {
    registration.periodicSync.register({
        tag: "同步事件名称",                 // 要保持唯一
        minPeriod: 12 * 60 * 60 * 1000,     // 两次成功同步的最小间隔
        powerState: "电池相关设置",          // 有"auto", "avoid-draining"两种
        networkState: "同步的网络需求"       // 有"online", "avoid-cellular", "any"
    }).then(periodicSyncreg => {

    })
})
```

## 第10章 流式数据
```
流的优势：
```
* 知道开始和结束
* 缓冲
* 通过管道连接
* 内置错误处理
* 可取消


可读流 = 推送源（可暂停、恢复数据流）+ 拉取源

```javascript
var stream = new ReadableStream({
    start(controller) {},   //会被立即调用并用来设置基础数据源（推送源|拉取源）
    pull(controller) {},    //会重复调用，直到缓冲区占满
    cancel(reason) {}       //消费者取消基础数据源
}, queuingStrategy)         //流的内部队列过载的处理策略
```

```
//流的说明代码，玩具代码
self.addEventListener("fetch", event => {
    event.respondWith(htmlStream());
})

function htmlStream(){
    const html = "html goes here ....";
    const stream = new ReadableStream({
        start: controller => {
            const encoder = new TextEncoder();
            let pos = 0;
            let chunkSize = 1;

            function push(){
                if (pos >= html.length){
                    controller.close();   //如果html被全扔进stream里面就关闭controller
                    return;
                }

                //将一个html chunk放进队列
                controller.enqueue(
                    encoder.encode(html.slice(pos, pos + chunkSize))
                )

                //更新索引
                pos += chunkSize;
                //循环
                setTimeout(push, 50);
            }
            
            push();
        }
    });

    return new Response(stream, {
        headers: {
            "Content-Type": "text/html"
        }
    })
}
```

## 第11章 PWA故障排除

```
这一部分有相当的都在前面已经有提过了，只挑一些特别的
```
* 可在Service Worker更新后的activate事件中清除代码缓存
* PWA存储使用情况：

```javascript
navigator.storage.estimate("temporary").then(function(info){
    console.log(info.quota)     //总的存储空间，字节为单位
    console.log(info.usage)     //到目前为止使用了多少，字节为单位
})
```
* 永久缓存：
```javascript
//这个api不一定有
navigator.storage.persist().then(granted => {
    if(granted) console.log("启动悠久缓存");
})
```

## 第12章 前程似锦
```
介绍一些未来的新api：
这些api用之前都要探测。
```

* 蓝牙api

```javascript
navigator.bluetooth.requestDevice({         // 请求访问附近的蓝牙设备
    acceptAllDevices: true,                 // 接受附近的所有设备
    optionalServices: ["battery_service"]   // 定义optionalServices以访问制定设备的服务
}).then(device => console.log(device.name)) // 输出设备详情
```

* Web分享API

```javascript
navigator.share({           // 通过手机的那个原生的方式分享
    title: "分享文章的标题",
    url: "分享的url"
}).then
```

* 支付请求API

```javascript
if(window.PaymentRequest){
    var paymentMethods = [{
        supportedMethods: ["basic-card"],
        data: { supportedNetworks: ["visa", "mastercard"] }
    }]
    var details = {
        displayItems: [{
            label: "",
            amount: {
                currency: "USD",
                value: "65.00"
            }
        }]
    }

    // 这个api有用的好像是只有这两句，展现了一个详情确定弹框给用户确认，
    // 真正的与后台的数据交互还得自己来干。
    var request = new PaymentRequest(paymentMethods, details);
    request.show().then(paymentResponse => {   
        var paymentData = {
            method: paymentResponse.methodName,
            details: paymentResponse.details
        }


        return fetch("/pay",{
            method: "post",
            credentials: "include",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify(paymentData)
        }).then(res => {
            //
        })
    })
}
```

* 形状检测api检测条形码

```javascript
var barcodeDetector = new BarcodeDetector();
barcodeDetector.detect(image)       // 可以是<img>, CanvasImageSource, imageData, blob
    .then(barcodes => {
        barcodes.forEach(barcode => console.log(barcode.rawValue));
    })
```

