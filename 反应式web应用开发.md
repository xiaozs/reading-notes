# 《反应式web应用开发》
## 第1章 你在谈论反应式编程吗
反应式的四项特质：
* 响应性：对用户的反应（更快的对用户的请求做出响应）
    * 基于事件的应用服务器（node之类的使用事件循环的服务器，有更高的性能，更大的吞吐）
    * 不可变状态、锁与竞态（用函数式并行模式，避免竞态带来的性能影响）
* 扩展性：对负载的反应（这里介绍了3中模型，首推第三种）
    * 单服务器部署（垂直扩展，要不断的提高单一一台计算机的性能）
    * 副本部署（部署多台服务器，并通过集群或类似的方法来共享状态，问题在于配置复杂）
    * 水平部署（部署多台服务器，不共享状态）
* 弹性：对故障的反应（介绍了一些处理故障的方法）
    * 有弹性的客户端（PWA）
    * 水密舱（微服务，避免一挂全挂）
    * 监管与Actor（不熟悉，要看接下来的内容）
* 事件驱动：对事件的反应
---
处理负载：
* little法则进行容量规划（利用监控监听访问量，做规划）
* 动态扩展与伸缩（利用监控监听访问量，自动调整云服务器容量）
* 回压传播（根据用户对数据的消费速度调整传输速度，例：看网络视频时的看一段下一段，不看就不下）
* 断路器（用于保护那些吞吐量较低的服务）