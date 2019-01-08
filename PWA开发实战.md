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