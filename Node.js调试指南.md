# 《Node.js调试指南》
## 第1章 CPU
```
介绍了火焰图、差分火焰图，用他们来进行性能分析
和一些生成相关的命令，这个实在是太难记了，而且linux限定。
```
火焰图的读法还是看这个比较易懂：[如何读懂火焰图？ - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2017/09/flame-graph.html)

差分火焰图用于修改前后的对比：执行的函数的次数增多则标红，减少则标蓝

```
本人更推荐用node的--inspect或--inspect-brk参数，
配合chrome的chrome://inspect/#devices，
来生成.cpuprofile文件
可以用npm上的flamegraph来生成火焰图
```
```
其余也介绍了v8-analytics这个工具，至于具体用法还得自己去找
```
```
node --prof xxx
可以生成性能日志

node --prof-process xxx.log
用于分析性能日志

里面有简单的阅读方法，但是像蜻蜓点水一样没有多少内容。
```