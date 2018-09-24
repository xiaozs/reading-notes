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
    //注册，注册文件为sw.js
    navigator.serviceWorker.register("/sw.js").then(() => {
        //注册成功
    }).catch(() => {
        //注册失败
    })
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