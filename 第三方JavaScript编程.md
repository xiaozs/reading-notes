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

## 第3章 HTML和CSS的渲染
* 输出HTML：document.write、innerHTML等
* 为你的HTML添加样式
    * 使用内联样式
    * 加载CSS文件
        * 浏览器样式闪烁
        * 确定CSS文件何时被加载（轮询页面并查找测试规则何时可用）或者使用css注入（```<style>```）
* 防御性的HTML和CSS
* 将内容嵌入到iframe中（讨论的都是啥时候该用iframe）

## 第4章 与服务器通信
* AJAX和浏览器的同源策略：同源策略的介绍
* 带填充的JSON（JSONP）
    * 仅适用于HTTP的GET请求
    * 缺乏错误处理机制
    * 有可能进行跨站请求伪造（CSRF）攻击
    * 总是异步的
* 子域名代理
* 跨源资源共享（CORS）

## 第5章 跨域iframe通信
* HTML5 window.postMessage API
    * IE8和IE9只允许向iframe元素发送消息
    * IE8和IE9也不支持上面提到的结构化克隆算法
* 降级技术
    * 使用window.name发送消息
    ```javascript
    // window.name有一个很有用的特点：
    // 一旦被赋值后，当窗口被重定向到一个新的URL时不会改变它的值
    // 这一行为使得不同域的特定文档可以读取该属性值，因此可以绕过同源策略并使跨域消息通信成为可能

    // 在发布者的页面创建一个iframe并将其URL指到你域名下的一个目标页面。
    // 当页面加载完成后，iframe会通过给window.name属性赋值，将消息字符串发送给父窗口。
    // 但是父页面并不能原样读取该值，因为iframe页面隶属一个不同的域（SOP）。
    // 为了解决这个问题，iframe需要将自身跳转到发布者域名的页面。
    // 当iframe重定向后，因为iframe当前打开的是同一个域下的文档，父页面就可以获取window.name的值。
    // 由于window.name属性在跳转后并不会发生改变，它依旧包含最初由外部页面设置的消息
    var iframe = docuemnt.createElement("iframe");
    var body = docuemnt.getElementsByTagName("body")[0];

    iframe.style.display = "none";
    iframe.src = ""
    
    var done = false;
    iframe.onreadystatechange = function(){
        if(iframe.readyState !== "complete" || done){
            return;
        }
        
        var name = iframe.contentWindow.name;
        if(name) {
            console.log(iframe.contentWindow.name);
            done = true;
        }
    }

    body.appendChild(iframe);
    ```
* 