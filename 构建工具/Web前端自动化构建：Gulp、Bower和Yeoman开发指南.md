# 《Web前端自动化构建：Gulp、Bower和Yeoman开发指南》
## 第1章 现代前端工作流的工具链
介绍了3类系统：
* 构建系统
* 依赖管理
* 脚手架工具

工作流：
* 初始化：<br>
搭建项目框架、套用模板代码、安装第三方软件
* 开发：<br>
运行代码编译工具、检查代码质量、运行开发服务器
* 部署：<br>
运行代码编译工具、压缩和混淆代码、
打包js和css、运行单元和验收测试、
上传文件和应用、删除调试代码、
检查代码质量、优化图片

## 第2章 Gulp入门
* 那些命令行工具提议把命令行提取出来做一个xxx-cli包是为了一个命令在不更新xxx-cli的情况下，可以执行放在node_modules里面的不同版本的xxx包。


* gulp.src创建文件可读流
* gulp.dest创建可写流
* 虚拟文件对象 = 文件名 + 内容
* gulp.pipe操纵虚拟文件对象

## 第3章 用Gulp配置本地开发环境
gulp4新api：
* gulp.series：串行执行任务
* gulp.parallel：并行执行任务
* gulp.watch：观察符合glob的文件的增删改，执行回调

（感觉gulp4是比gulp3执行会更快，但是配置更复杂了）

## 第4章 Bower依赖管理
* 嵌套依赖树：npm2
* 扁平依赖树：bower
* 半嵌套／半扁平的依赖树：npm3

bower解决冲突的办法：<br>
bower.json文件里面记载了当前下载了的依赖版本，<br>
当安装新模块时，新模块的bower.json文件中的依赖会和当前下载了的做对比<br>
若果有冲突，命令行会弹出选项<br>
(也就是说，某些情况下还是会存在完全解决不了的依赖冲突，可以说再见了)

最后是，配合gulp做的构建，其原理是：
使用插件分析bower.json文件，生成依赖文件的path数组，<br>
之后是把它们合并在一起也好，直接注入到html里面也好，怎么都行了。

## 第5章 脚手架工具Yeoman
```
可以联动《JavaScript开发框架权威指南》来理清一个generator的编写思路。
```

项目里面的文件：
* 固定文件：在每个项目里都一样的文件。<br>
构建脚本、入口文件等。
* 灵活文件：会根据不同项目而改变。<br>
根据手脚架用户选项改变的文件
* 可选文件：可要可不要的文件。
* 可恢复文件：package.json记载的依赖文件。

Yeoman 的组装流程：
* 安装提示：通过向用户提问，来让用户自定义配置
* 写入文件：从项目模板中把文件复制到目录中
* 安装依赖



## 第6章 不同环境下的Gulp
gulp4的新api：

```javascript
gulp.src(glob, {
    since: time //时间戳，挑选出增改在这个时间之后的文件
})

gulp.lastRun(taskName) //taskName上次运行的时间戳

//上面的2个api的组合技能，只能对一对一的文件处理流程有用

//要对多对1的流程，可配合gulp-cached和gulp-remember使用
//还要配合watcher.on("unlink", cb)来监察文件的删除
//从gulp-cached删除文件
```
这部分应该是整本书最复杂的地方了，通过这个步骤就可以搭建可以增量构建的环境了。

## 第7章 使用流
* 穿插流：在原有的流程的中途添加新内容。
* 合并流：将两个流合并成一个。

（其实穿插流和合并流是一个东西，只不过穿插流的合并时机是它一生成的时候）
```
这一章书的内容也很复杂。
主要讲述了gulp中流的各种用法，以实现目的，减少代码重复。

挺有复习价值的。
```

## 第8章 扩展Gulp

vinyl(虚拟文件对象)有下面的属性：
* base：文件名
* path：文件路径
* content：文件内容（buffer或者stream）

这一章的内容比较难，由于gulp是基于流的任务系统，<br>
所以为了使用一些不是纯流操作的插件<br>
这章书介绍了一些stream、buffer、promise之间的转换工具

联想到stream、buffer、promise、eventEmitter、obserable、iterable等一系列东西之间的关联，<br>
深挖下去可能会有意想不到的收获，建议复习


## 第9章 创建模块和Bower组件

介绍了amd、cmd、umd、es2015等标准，这个没有什么好说的。<br>
然后是bower.json文件的各个字段描述<br>
git和github<br>
最后是发布的命令行<br>
和一些使用私有代码的方法。


## 第10章 Yeoman generator 进阶
介绍了Yeoman的sub-generator的写法，<br>
使用.yo-rc.json文件存储用户选择、<br>
使用原生api打包别人的generator包以满足自己的需求<br>
等小技巧<br>