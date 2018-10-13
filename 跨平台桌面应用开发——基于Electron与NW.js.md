# 《跨平台桌面应用开发——基于Electron与NW.js》
## 第1章 Electron和NW.js入门
```
简单介绍了使用Electron和NW.js的理由：
像是java一样的一次编写到处执行
比网页能做更多的东西

还有两者的hello world应用
```

## 第2章 为你的首款桌面应用搭建开发环境

```
又复习了一边hello world，nw的很简单，
electron的姑且列一下：
```

```javascript
const electron = require("electron");
const app = electron.app;
const BrowserWindow = electron.BrowserWindow;

let mainWindow = null;
app.on("window-all-closed",() => {
    if(process.platform !== "darwin") app.quit();   //如果不是苹果电脑，就退出应用
})
app.on("ready", () => {
    mainWindow = new BrowserWindow();
    mainWindow.loadURL("file://${app.getAppPatch()/index.html}")
    mainWindow.on("closed", () => {
        mainWindow = null;
    })
})
```

## 第3章 构建你的首款桌面应用

```
以一个文件浏览器为例子，介绍了开发方式，只要动一点node和前端就能做，比较简单
唯一有点设计api的只有这个：
```

```javascript
//同一套代码同时可以适配electron和nw，我觉得没有什么用处
let shell;
if(process.versions.electron) {
    shell = require("electron").shell;
} else {
    shell = window.require("nw.gui").shell
}

shell.openItem(filePath)    //通过文件路径来打开调用api打开文件
```
