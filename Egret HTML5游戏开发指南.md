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
//todo这个流程稍微有点复杂，而且很不自动化

* resource项目资源目录、resource/*.json资源清单文件

```
还介绍了相关的命令行，由于都在界面上有，那就不记录了。
```

## 第4章 游戏的基础知识

* **显示对象**：参与渲染的对象<br>
类似于HTML里面的DOM的感觉，树形结构，每个显示对象里面有一个渲染列表，可以添加、删除子元素，<br>
子元素依据顺序的不同，后面的放上边，<br>
同时用一个叫Stage（舞台）的显示对象作为root，<br>
Stage会根据帧率，每隔一段事件向子元素递归发送事件，<br>
子元素可以监听这个事件，改变自己的坐标啊等等的参数<br>
下一个事件循环获取所有元素的结果，经过相应的映射（应该有的、类似于缩放啊这些效果），<br>
渲染到canvas上，以达到动画效果。

* **graphics**：每个显示对象都有的一个属性，可调用以进行画画。<br>
稍微看了一下这个对象的api，跟canvas原生的绘图api高度相似，应该只是一层简单封装。

* **遮罩**：每个显示对象上有一个mask属性<br>
给这个属性赋值一个显示对象或者一个Rectangle，<br>
那么这个对象在渲染的时候就只会渲染重叠的部分<br>
这个是这么做到的我暂时还不怎么清楚。

* **非精确碰撞检测**：也就是通过包围盒判断一个坐标，是否在盒子内。
* **精确碰撞检测**：查看一个坐标所在的像素点的aplha是否为0，不为0就是碰撞了。
* **混合模式**：描述重叠图像的，重叠部分的渲染方式，有NORMAL、ADD、ERASE三种模式，<br>
我记得原生api里面好像有这部分的东西，上面的遮罩可能也是由这个实现的。

## 第5章 事件与用户交互
* 在egret中的事件模型和dom极为相似，<br>
事件会以stage为根，沿着每个显示列表往下传播，然后往回冒泡<br>
其中Event对象的属性也和dom非常像。

比较有特色的地方在于，对事件捕获阶段，目标阶段和事件冒泡阶段3个阶段的区分比较细，<br>
而且以捕获阶段优先，<br>
还提供了一个额外的参数以定义事件优先级
```
总体来说这个事件模型跟dom的很想、可以参考一下
```