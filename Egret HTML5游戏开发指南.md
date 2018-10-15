# 《Egret HTML5游戏开发指南》

## 第1章 欢迎来到HTML5的世界
```
说了一些用HTML5做游戏的原因
```
* 即点即玩
* 社交化
* 跨平台
* 开放


## 第2章 奇妙的前端之旅
```
吹了一波Egret
```


## 第3章 Hello Egret
* Egret Wing: IDE工具
* ResDepot：资源管理工具
* Texture Merger：资源打包工具
```
这货就是一个自定义的vscode，而且环境变量还没搞好，跑不起来
/Egret/EgretLauncher/resources/app/engine/win/bin
```
```
文件介绍：
```
* Main.ts 项目入口类：<br>
项目的html里面有一小段加载代码，<br>
会读取manifest.json里的文件进行加载，<br>
这些文件会把东西直接插到全局变量里面，包括Main.ts里面的类，<br>
通过div上的data-entry-class从window里面拉出Main类来，<br>
执行它的createChildren方法，开始运行

* egretProperties.json项目配置文件<br>
这个文件的配置，配合```egret build```命令会生成<br>
libs文件夹下面的文件和manifest.json，<br>
src下的文件也会编译到bin-debug下<br>
egret分为模块egret\dragonbones\eui\game\gui\res\socket\tween几个核心模块,<br>
可以按需要引入

* 第三方模块<br>
在工程外执行```egret create_lib <packageName>```<br>
//todo这个流程稍微有点复杂

* resource项目资源目录、resource/*.json资源清单文件

```
还介绍了相关的命令行，由于都在界面上有，那就不记录了。
```