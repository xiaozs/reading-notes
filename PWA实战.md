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
