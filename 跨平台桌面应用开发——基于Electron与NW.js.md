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

## 第4章 分发你的首款桌面应用
```
第一节
是对应用图标的一些说明，这个不重要，之后再说

第二节
nw-builder
electron-builder 
这两个npm包可以用来构建app，

构建app，对生成的各个app文件通过工具来修改它们的应用图标
```

---
```
老实说，这本书的前边4章接近100页，整书的三分之一都没有什么营养。
```

## 第5章 在NW.js和Electron中使用Node.js

```
一整章都是在介绍node，没有什么大用，

介绍了一个npm命令，用于锁定依赖版本：
```
```
npm shrinkwrap
```


## 第6章 探索NW.js和Electron的内部机制
```
讲解了nw和Electron的内部原理，
由于它们之间的不同，
导致nw可以在同一个上下文中访问node和浏览器的api，
Electron要通过特殊的方式进行交流，

这一章只是走马观花的说了一下。
```


## 第7章 自定义桌面应用的外观
* 设置窗口大小
```javascript
//nw
//package.json
{
    "window": {
        "width": 300,
        "height": 200,
        "max_width": 1024,
        "min_width": 800,
        "max_height": 768,
        "min_height": 600
    }
}

//代码
const gui = require("nw.gui");
const win = gui.Window.get();

win.width = 1024;
win.height = 768;

//设置窗口坐标
win.x = 400;
win.y = 500;

```
```javascript
//Electron
app.on("ready", () => {
    mainWindow = new BrowserWindow({
        width: 400,
        height: 200,
        maxWidth: xxx,
        minWidth: xxx,
        maxHeight: xxx,
        minHeight: xxx,
        x: xxx,
        y: xxx
    })
})
```

* 全屏
```javascript
//nw
{
    "window": {
        "fullscreen": true
    }
}

//这玩意貌似有bug，有时需要html的全屏api
const gui = require("nw.gui");
const window = gui.Window.get();
window.enterFullscreen();
window.leaveFullscreen();
```
```javascript
//Electron
//初始化
mainWindow = new BrowserWindow({ 
    fullscreen: true,
    fullscreenable: true
});
//代码
const remote = require("electron").remote;
const win = remote.getCurrentWindow();
win.isFullScreen();
win.setFullScreen(flag);
```

* 窗口边框
```javascript
//nw
{
    "window": {
        "frame": false,
        "transparent": true //背景透明
    }
}
//html元素要设置css，才能拖动
-webkit-app-region: drag;
```
```javascript
//Electron
mainWindow = new BrowserWindow({ 
    frame: false,
    transparent: true
});
```

* kiosk应用（那种银行、医院、图书馆开了就不能关，只能干这个事的那种应用）

```javascript
//nw
{
    "window": {
        "kiosk": true
    }
}
//代码
const gui = require("nw.gui");
const win = gui.Window.get();
win.leaveKioskMode();

```

```javascript
//Electron
mainWindow = new BrowserWindow({
    kiosk: true
});
const remote = require("electron").remote;
const win = remote.getCurrentWindow();
win.isKiosk();
win.setKiosk(flag);
```

## 第8章 创建托盘应用

```javascript
//nw
const gui = require("nw.gui");
const tray = new gui.Tray({ 
    title: "My tray app",   //文字的只能在苹果上用
    icon: "图片url"         //放在index.html同级目录下
});
//托盘菜单
const note = [
    { title: "菜单标题", click:() => { //todo } }
]
const menu = new gui.Menu();
notes.forEach(note = >{
    menu.append(new gui.MenuItem({
        label:note.title
        click:note.click
    }))
})
tray.menu = menu;
```
```javascript
const electron = require("electron");
const app = electron.app;
const Menu = electron.Menu;
const tray = electron.Tray;
const BrowserWindow = electron.BrowserWindow;

let appIcon = null;
let mainWindow = null;

const note = [
    { 
        title: "菜单标题", 
        click:() => {
            //todo,electron要涉及到ipc，之后应该有介绍，这里就算了
        }
    }
]

app.on("ready", () => {
    appIcon = new Tary("icon@xx.png");  //创建带图标托盘
    let contextMenu = Menu.buildFromTemplate(notes.map(note => ({
        label:note.title,
        type:"normal",
        click:note.click
    })));
    appIcon.setToolTip("图标的工具提示");
    appIcon.setContextMenu(contextMenu);
})
```

## 第9章 创建应用菜单以及上下文菜单
```javascript
//平台判断代码
const os = require("os");
os.platform(); // => "darwin" //这个是苹果
```
* 抬头菜单

```javascript
//nw
//apple
const gui = require("nw.gui");
const mb = new gui.Menu({ type: "menubar" });
mb.createMacBuiltin("example");
gui.Window.get().menu = mb;
```
```javascript
//electron
//apple
const electron = require('electron');
const Menu = electron.remote.Menu;
const name = electron.remote.app.getName();

const template = [{
    label: '',  //mac上空字符串会变成应用名
    submenu: [
        {
            label: 'About ' + name,
            role: 'about'   //声明类型，指定默认行为
        },
        {
            type: 'separator'   //分隔符
        },
        {
            label: 'Quit',
            accelerator: 'Command+Q', //快捷键
            click: electron.remote.app.quit
        }
    ]
}];

const menu = Menu.buildFromTemplate(template);
Menu.setApplicationMenu(menu);
```

```javascript
//nw
//Windows/Linux
const gui = require('nw.gui');
const menuBar = new gui.Menu({ type: 'menubar' });
const fileMenu = new gui.MenuItem({ label: 'File' });

const sayHelloMenuItem = new gui.MenuItem(
    {
        label: 'Say hello',
        click: () => { alert('Hello'); }
    }
);

const quitAppMenuItem = new gui.MenuItem(
    {
        label: 'Quit the app',
        click: () => { process.exit(0); }
    }
);

// 还是挺容易的，不过这里的嵌套太烦了吧
const fileMenuSubMenu = new gui.Menu();
fileMenuSubMenu.append(sayHelloMenuItem);
fileMenuSubMenu.append(quitAppMenuItem);

fileMenu.submenu = fileMenuSubMenu;

menuBar.append(fileMenu);
gui.Window.get().menu = menuBar;
```

```javascript
//electron
//Windows/Linux
//其实electron的Windows/Linux/mac代码都一样的
const electron = require('electron');
const Menu  = electron.remote.Menu;

const sayHello = () => { alert('Hello'); };

const quitTheApp = () => { electron.remote.app.quit(); };

const template = [
  {
    label: 'File',
    submenu: [
      {
        label: 'Say Hello',
        click: sayHello
      },
      {
        label: 'Quit the app',
        click: quitTheApp
      }
    ]
  }
];

const menu = Menu.buildFromTemplate(template);
Menu.setApplicationMenu(menu);
```

* 上下文菜单(右键菜单)
```javascript
//nw
const gui = require('nw.gui');
const menu = new gui.Menu();
menu.append(new gui.MenuItem({
    icon: 'picture.png',
    label: 'Insert image',
    click: ()=>{} 
}));
el.addEventListener('contextmenu',(event) => {
    event.preventDefault();
    x = event.x;
    y = event.y;
    menu.popup(event.x, event.y);
    return false;
});
```
```javascript
//electron
var electron = require('electron');
var Menu = electron.remote.Menu;
var MenuItem = electron.remote.MenuItem;
const menu = new Menu();
menu.append(new MenuItem({
    label: 'label',
    click: () => {}
}));
el.addEventListener('contextmenu',(event) => {
    event.preventDefault();
    x = event.x;
    y = event.y;
    menu.popup(event.x, event.y);
    return false;
});
```


## 第10章 拖曳文件以及定制界面
```javascript
//禁用浏览器的默认行为

function stopDefaultEvent(event) {
    event.preventDefault();
    return false;
}
window.addEventListener("dragover", stopDefaultEvent);
window.addEventListener("drop", stopDefaultEvent);

```
总而言之，nw和electron的拖曳api和html5的完全一样，<br>
这章书主要介绍的是一些为了达到与系统的样式一致的css框架


## 第11章 在应用中使用网络摄像头
也是html5 api

```javascript
window.navigator.webkitGetUserMedia(
    { video: true },
    localMediaStream => {
        //video是一个<video>
        video.src = window.URL.createObjectURL(localMediaStream);
        video.addEventListener('loadedmetadata', callback);
    },
    errorCallback
)
```
```javascript
//electron
//可以在浏览器里弹出保存框

const electron = require('electron');
const dialog = electron.remote.dialog;

dialog.showSaveDialog({
    title: "Save the photo",
    defaultPath: 'myfacebomb.png',
    buttonLabel: 'Save photo'
}, callback);
```


## 第12章 存储应用数据
这一章就更水了就用了个localStorage。

存储数据的可选方案：
|名字|数据库类型|类型|
|---|---|---|
|IndexedDB|键值|浏览器api|
|localStorage|键值|浏览器api|
|Lovefield|关系型|客户端库|
|PouchDB|文档型|客户端库|
|SQLite|关系型|内嵌|
|NeDB|文档型|内嵌|
|LevelDB|键值|内嵌|
|Minimongo|文档型|客户端库|