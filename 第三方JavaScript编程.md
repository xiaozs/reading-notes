# 《第三方JavaScript编程》
## 第1章 第三方JavaScript介绍
从第三方服务器加载的代码

* 内嵌微件：内嵌在发布者网站上的小型交互式应用
* 统计分析：收集访客在发布者网站上的交互数据
* Web服务API封装：开发同外部Web服务通信的客户端应用

---
第三方开发的挑战：
* 未知的上下文：你不知道js的形成
* 共享环境：你不知道环境的api是否被覆盖过等
* 浏览器限制：ajax跨域等

## 第2章 应用的分发和加载
* 配置第三方开发环境
    * 发布者的测试页面
    * 服务器
    * 模拟多个域（修改host）
* 加载初始的脚本
    * 阻塞式脚本引入（可以使用document.write）
    * 使用async和defer无阻塞加载脚本
        * defer：当浏览器完成页面的加载后，会按顺序执行延迟加载的这些脚本
        * async：一旦在它们下载完毕后便会被执行（ie10+）
    * 动态脚本插入
        ```javascript
        (function(){
            var script = document.ctreateElement("script");
            script.src = "";
            script.async = true;    // 让部分旧浏览器支持异步加载
            var entry = document.getElementsByTagName("script")[0];
            entry.parentNode.insertBefore(script, entry);
        })();
        ```
* 初始脚本文件
    ```javascript
    (function(window, undefined){
        // window, undefined 会被压缩软件压缩
    })(window)
    ```
* 加载额外的文件
    ```javascript
    // 用于加载依赖文件，让我们的插件只需要一个<script>就能启动
    function loadScript(url, callback){
        var script = document.ctreateElement("script");

        script.src = url;
        script.async = true;

        var entry = document.getElementsByTagName("script")[0];
        entry.parentNode.insertBefore(script, entry);

        script.onload = script.onreadystatechange = function () {
            var rdyState = script.readyState;

            if(!rdyState || /complete|loaded/.test(script.readyState)){
                callback();
                script.onload = null;
                script.onreadystatechange = null;
            }
        }
    }
    ```
* 脚本参数传递
    * 使用查询字符串
        * 通过脚本的URL查询字符串传递参数，动态生成js
        * 或通过遍历```<script>```的src找到自己的url得到参数
        * JavaScript文件很难被缓存
    * 使用片段标识符
    * 使用自定义数据属性```data-*```
        * 还可以用这个属性找到```<script>```
    * 使用全局变量

* 获取应用数据(第4章)