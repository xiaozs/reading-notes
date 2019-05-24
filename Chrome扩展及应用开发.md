# Chrome扩展及应用开发
## 第1章 初步接触Chrome扩展应用开发
Manifest文件格式：
```json
{
    "app": {
        "background": {
            "scripts": ["background.js"]
        }
    },

    // 目前只能为数字2
    "manifest_version": 2,
    "name": "My Extension",
    "version": "versionString",
    "default_locale": "en",
    "description": "A plain text description",
    "icons": {
        "16": "images/icon16.png",
        "48": "images/icon48.png",
        "128": "images/icon128.png"
    },
    "browser_action": {
        "default_icon": {
            "19": "images/icon19.png",
            "38": "images/icon38.png"
        },

        // 用户鼠标悬停于扩展图标上所显示的文字
        "default_title": "Extension Title",

        // 用户单击扩展图标时所显示页面
        "default_popup": "popup.html"
    },
    "page_action": {
        "default_icon": {
            "19": "images/icon19.png",
            "38": "images/icon38.png"
        },
        "default_title": "Extension Title",
        "default_popup": "popup.html"
    },

    // 后台页面
    "background": {
        "scripts": ["background.js"],

        // 指定的HTML文件作为后台页面运行, 
        // 通常我们只需要使用scripts属性即可，除非在后台页面中需要构建特殊的HTML
        "page": "",

        // 常驻后台的方式
        // 当其值为true时，表示扩展将一直在后台运行，无论其是否正在工作
        // 当其值为false时，表示扩展在后台按需运行
        "persistent": true
    },

    // 指定将哪些脚本何时注入到哪些页面中
    "content_scripts": [
        {
            "matches": ["http://www.google.com/*"],
            "css": ["mystyles.css"],
            "js": ["jquery.js", "myscript.js"],

            // 定义了何时进行注入
            "run_at": [],

            // 是否会注入到嵌入式框架
            "all_frames": true,

            // 最终脚本是否会被注入由matches、exclude_matches、include_globs和exclude_globs的值共同决定
            "exclude_matches": [],
            "include_globs": [],
            "exclude_globs": []
        }
    ],

    // 当用户在扩展图标上点击右键，选择菜单中的“选项”后，就会打开这个页面
    "options_page": "options.html",

    "permissions": [
        // 跨域的权限
        "*://www.google.com/*",
        // Chrome存储API的权限
        "storage"
    ],
    "web_accessible_resources": [
        "images/*.png"
    ]
}
```
## 第2章 Chrome扩展基础
api文档：http://developer.chrome.com/extensions/extension

```javascript
// 动态改变扩展图标
chrome.browserAction.setIcon({ path: "" });
```
```javascript
// 扩展页面间的通信
chrome.runtime.sendMessage(
    // 可选, 目标扩展，如果不指定这个值，则默认为发起此消息的扩展本身
    extensionId, 
    // 可以是对象
    message, 
    // 可选
    options, 
    // 回调
    function(
        // 回复内容
        response
    ){ }
);
chrome.runtime.onMessage.addListener(function(
    message,
    sender,
    sendResponse
) { 
    sendResponse(response /* 回复内容 */);
})
runtime.connect
runtime.onConnect
```
```javascript
// Chrome存储API

// 如果储存区域指定为sync，数据可以自动同步
// content_scripts可以直接读取数据，而不必通过background页面
// 在隐身模式下仍然可以读出之前存储的数据
// 读写速度更快
// 用户数据可以以对象的类型保存

// 使用Chrome存储API必须要在Manifest的permissions中声明"storage"

// Chrome存储API提供了2种储存区域，分别是sync和local。
// 两种储存区域的区别在于，sync储存的区域会根据用户当前在Chrome上登陆的Google账户自动同步数据，
// 当无可用网络连接可用时，sync区域对数据的读写和local区域对数据的读写行为一致

chrome.storage.sync.get(keys, callback)
chrome.storage.sync.set(keys, callback)
chrome.storage.sync.remove(keys, callback)
chrome.storage.sync.clear(callback)
// 获取一个数据或多个数据所占用的总空间，返回结果的单位是字节
chrome.storage.sync.getBytesInUse(keys, callback)

chrome.storage.local.get(keys, callback)
chrome.storage.local.set(keys, callback)
chrome.storage.local.remove(keys, callback)
chrome.storage.local.clear(callback)
chrome.storage.local.getBytesInUse(keys, callback)


chrome.storage.onChanged.addListener(function (
    // 词典对象，键为更改的属性名称，值包含两个属性，分别为oldValue和newValue
    changes,
    // local或sync
    StorageArea
) { })
```

