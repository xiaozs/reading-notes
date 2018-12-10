# 《Git版本控制管理（第2版）》
## 第1章 介绍
* 有助于分布式开发
* 能够胜任上千开发人员的规模
* 性能优异（为了节约存储空间，从而节约传输时间，需要使用“压缩”和“差异比较”技术）
* 保持完整性和可靠性（用加密散列函数来命名和识别数据库中的对象）
* 强化责任
* 不可变性
* 原子事务
* 支持并且鼓励基于分支的开发
* 完整的版本库
* 一个清晰的内部设计
* 免费自由

## 第2章 安装git
linux上的安装比较复杂，还有很多实现不同功能的包。<br>
windows上的则很简单。



## 第3章 起步
* ```git init```初始化版本库
* ```git add <fileName>```将文件添加到版本库中
* ```git status```查看版本库状态
* ```git commit -m <commitMesage>```提交(GIT_EDITOR,编辑器)
* ```git config user.name <userName>```配置提交作者姓名(GIT_AUTHOR_NAME)
* ```git config user.email <userEmail>```配置提交作者email(GIT_AUTHOR_EMAIL)
* ```git log```查看提交
* ```git show <commit hash>```查看提交的详细信息
* ```git show-branch --more=<showCount>```提供当前开发分支简洁的单行摘要
* ```git diff <hash1> <hash2>```查看提交差异
* ```git rm <fileName>```删除这个文件并暂存这个变更
* ```git mv <fileName>```重命名这个文件并暂存这个变更
* ```git clone <fromPath> <toPath>```

* 配置文件
    1. .git/config 版本库特定的配置设置，可用--file选项修改，是默认选项。这些设置拥有最高优先级。
    2. ~/.gitconfig 用户特定的配置设置，可用--global选项修改
    3. /etc/gitconfig 系统范围的配置设置，可用--system选项修改它
    4. ```git config -l```列出所有设置值
    5. ```git config --unset <configName>````移除设置

## 第4章 基本的Git概念
* 版本库
    * 对象库
    * 索引
* Git对象类型
    * 块（blob）：文件的每一个版本表示为一个块（不包含文件的元数据）
    * 目录树（tree）：代表一层目录信息,记录在一个目录里所有文件的元数据
    * 提交（commit）：保存每一次变化的元数据。每一个提交对象指向一个目录树对象。大多数提交都有一个父提交。
    * 标签（tag）：一个标签对象分配一个任意的且人类可读的名字给一个特定对象。
* 索引：会记录和保存那些变更