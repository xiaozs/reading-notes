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

    //类似于browser_action
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
        "storage",
        // 访问 右键菜单
        "contextMenus",
        // 桌面提醒
        "notifications",
        // 操作菜单栏
        "bookmarks",
        // 操作cookies
        "cookies",
        // 历史纪录
        "history",
        // 管理扩展与应用
        "management",
        // 标签
        "tabs",

        // 下载
        "downloads",
    ],

    // 桌面提醒等 中显示的图片
    "web_accessible_resources": [
        "images/*.png"
    ],

    // 启动omnibox的keyword
    "omnibox": { "keyword" : "keyword" }
}
```
## 第2章 Chrome扩展基础
api文档：http://developer.chrome.com/extensions/extension

```javascript
// 动态改变扩展图标
chrome.browserAction.setIcon({
    // 可以是下2个属性其一
    path: "",
    // imageData, 格式跟Manifest一样
    {
        19: "",
        39: ""
    },

    //限定了浏览哪个标签页时，图标将被更改
    tabId
}, callback);

// 动态改变扩展标题
chrome.browserAction.setTitle({title: 'This is a new title'});

// 设置Badge
chrome.browserAction.setBadgeBackgroundColor({color: '#0000FF'});
chrome.browserAction.setBadgeBackgroundColor({color: [0, 255, 0, 128]});
chrome.browserAction.setBadgeText({text: 'Dog'});
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

## 第3章 Chrome扩展的UI界面
```javascript
// 右键菜单
// 通常create方法由后台页面来调用，即通过后台页面创建自定义菜单。
// 如果后台页面是Event Page，通常在onInstalled事件中调用create方法。

// 普通菜单
chrome.contextMenus.create({
    type: 'normal',
    title: 'Menu A',
    id: 'a'
});

// 单选菜单
chrome.contextMenus.create({
    type: 'radio',
    title: 'Menu B',
    id: 'b',
    checked: true
});

chrome.contextMenus.create({
    type: 'radio',
    title: 'Menu C',
    id: 'c'
});

// 复选菜单
chrome.contextMenus.create({
    type: 'checkbox',
    title: 'Menu D',
    id: 'd',
    checked: true
});

// 分割线
chrome.contextMenus.create({
    type: 'separator'
});


// 子菜单
chrome.contextMenus.create({
    type: 'normal',
    title: 'Menu F',
    id: 'f',
    parentId: 'a'
});


chrome.contextMenus.create({
    type: 'normal',
    title: 'My Menu',

    // 右键菜单在何时显示
    contexts: [
        // 不包含launcher
        "all",

        // 默认，所有的页面唤出右键菜单时都显示自定义菜单
        "page",
        "frame",
        "selection",
        "link",
        "editable",
        "image",
        "video",
        "audio",

        // 只对Chrome应用有效
        "launcher"
    ],
    // 允许限定页面的URL
    documentUrlPatterns: [],
    // 类似于documentUrlPatterns，诸如图片、视频和音频等资源的URL
    targetUrlPatterns: [], 

    // 点击回调
    onclick: callback
});


chrome.contextMenus.update
chrome.contextMenus.remove
chrome.contextMenus.removeAll
```

```javascript
// 桌面提醒
var notification = webkitNotifications.createNotification(
    'icon48.png',
    'Notification Demo',
    'Merry Christmas'
);
notification.show();
notification.cancel();

notification.addEventListener("ondisplay" | "onerror" | "onclose" | "onclick", callback);

// 也可以通过Chrome提供的chrome.notifications方法来创建功能更加丰富的提醒框。
```

```javascript
// Omnibox，多功能框，地址框

// 定义默认建议
chrome.omnibox.setDefaultSuggestion([{
    'content': "",
    'description': ""
},{
    'content': "",
    'description': ""
}])

chrome.omnibox.onInputChanged.addListener(function(text, suggest){
    suggest([{
        content: text,
        description: 'Search '+text+' in Wikipedia'
    }]);
});

chrome.omnibox.onInputStarted
chrome.omnibox.onInputChanged
chrome.omnibox.onInputEntered
chrome.omnibox.onInputCancelled
```

```javascript
// Page Actions
// Page Actions并不像Browser Actions那样一直显示图标，而是可以在特定标签特定情况下显示或隐藏，
// 所以它还具有独有的show和hide方法。
chrome.pageAction.show(integer tabId);
chrome.pageAction.hide(integer tabId);
```

## 第4章 管理你的浏览器
```javascript
// 创建书签
// id, 不需要人为干预
// parentId
// index
// url
// title
// dateAdded, 新增时间戳
// dateGroupModified, 修改时间戳
// children
chrome.bookmarks.create({
    parentId: '1',
    index: 0,
    title: 'Google',
    url: 'http://www.google.com/'
}, function(bookmark){
    console.log(bookmark);
});

// 调整书签的位置
chrome.bookmarks.move('16', {
    parentId:'7',
    index:4
}, function(bookmark){
    console.log(bookmark);
});

// 修改书签
chrome.bookmarks.update('16', {
    title: 'Gmail',
    url: 'https://mail.google.com/'
}, function(bookmark){
    console.log(bookmark);
});

// 移除书签
chrome.bookmarks.remove('16', function(){
    console.log('Bookmark 16 has been removed.');
});

chrome.bookmarks.removeTree('6', function(){
    console.log('Bookmark group 6 has been removed.');
});

// 获得用户完整的书签树
chrome.bookmarks.getTree(function(bookmarkArray){
    console.log(bookmarkArray);
});

// 获得以指定节点为父节点的下一级书签节点，但不包括再下一级的节点
chrome.bookmarks.getChildren('0', function(bookmarkArray){
    console.log(bookmarkArray);
});

// 获得自指定节点开始包括当前节点及向下的所有节点
chrome.bookmarks.getSubTree('0', function(bookmarkArray){
    console.log(bookmarkArray);
});

// 返回指定节点不包含children属性的书签对象数组
chrome.bookmarks.get(['16', '17'], function(bookmarkArray){
    console.log(bookmarkArray);
});

// 获取最近添加的多个书签
chrome.bookmarks.getRecent(5, function(bookmarkArray){
    console.log(bookmarkArray);
});

// 返回所有标题或URL中包含
chrome.bookmarks.search('google', function(bookmarkArray){
    console.log(bookmarkArray);
});

// 监控书签的创建行为
chrome.bookmarks.onCreated.addListener(function(bookmark){
    console.log(bookmark);
});

// 监控书签的移除行为
chrome.bookmarks.onRemoved.addListener(function(id, removeInfo){
    console.log('Bookmark '+id+' has been removed:');
    console.log(removeInfo);
});

// 监控书签的更新行为
chrome.bookmarks.onChanged.addListener(function(id, changeInfo){
    console.log('Bookmark '+id+' has been changed:');
    console.log(changeInfo);
});

// 监控书签的移动行为
chrome.bookmarks.onMoved.addListener(function(id, moveInfo){
    console.log('Bookmark '+id+' has been moved:');
    console.log(moveInfo);
});

// 以监控一个书签分组下的更改子节点顺序的行为
chrome.bookmarks.onChildrenReordered.addListener(function(id, reorderInfo){
    console.log('Bookmark '+id+' has a new children order:');
    console.log(reorderInfo);
});

// 监控导入书签开始和结束的行为
chrome.bookmarks.onImportBegan(function(){
    console.log('Bookmark import began.');
});

chrome.bookmarks.onImportEnded(function(){
    console.log('Bookmark import ended.');
});
```


```javascript
// cookies
chrome.cookies.get({
    url: 'https://github.com',
    name: 'dotcom_user'
}, function(cookie){
    console.log(cookie.value);
});

// 匹配条件包括url、name、domain、path、secure、session和storeId中的任意一个或多个
chrome.cookies.getAll({}, function(cookies){
    console.log(cookies);
});


chrome.cookies.set({
    'url':'http://github.com/test_cookie',
    'name':'TEST',
    'value':'foo',
    'secure':false,
    'httpOnly':false
}, function(cookie){
    console.log(cookie);
});

chrome.cookies.remove({
    url: 'http://www.google.com',
    name: '_ga'
}, function(result){
    console.log(result);
});

// 监控cookie的设置和删除行为
chrome.cookies.onChanged.addListener(function(changeInfo){
    console.log(changeInfo);
});
```


```javascript
// 历史

// 最近24小时内匹配“Google”的20条历史结果。
chrome.history.search({
    text: 'Google',
    startTime: new Date().getTime()-24*3600*1000,
    endTime: new Date().getTime(),
    maxResults: 20
}, function(historyItemArray){
    console.log(historyItemArray);
});

// 绝对匹配指定的URL
chrome.history.getVisits(
    url: 'http://www.google.com/'
}, function(visitItemArray){
    console.log(visitItemArray);
});

// 添加历史
chrome.history.addUrl({
    url: 'http://twitter.com'
}, function(){
    console.log('Twitter has been added to history.');
});

// 删除指定URL的历史
chrome.history.deleteUrl({
    url: 'http://www.google.com'
}, function(){
    console.log('Google has been deleted from history.');
});

// 删除指定时间段的历史
chrome.history.deleteRange({
    startTime: new Date().getTime()-24*3600*1000,
    endTime: new Date().getTime()
}, function(){
    console.log('History in past 24 hours has been deleted.');
});

// 删除全部历史
chrome.history.deleteAll(function(){
    console.log('All history has been deleted.');
});

// 访问历史
chrome.history.onVisited.addListener(function(historyItem){
    console.log(historyItem);
});

// 历史被删除
chrome.history.onVisitRemoved.addListener(function(removedObject){
    console.log(removedObject);
});
```


```javascript
// 管理扩展与应用

// 读取用户已安装扩展和应用
chrome.management.getAll(function(exInfoArray){
    console.log(exInfoArray);
});

chrome.management.get(exId, function(exInfo){
    console.log(exInfo);
});

// exInfo
{
    id: 扩展id,
    name: 扩展名称,
    shortName: 扩展短名称,
    description: 扩展描述,
    version: 扩展版本,
    mayDisable: 是否可被用户卸载或禁用,
    enabled: 是否已启用,
    disabledReason: 扩展被禁用原因,
    type: 类型,
    appLaunchUrl: 启动url,
    homepageUrl: 主页url,
    updateUrl: 更新url,
    offlineEnabled: 离线是否可用,
    optionsUrl: 选项页面url,
    icons: [{
        size: 图片尺寸,
        url: 图片URL
    }],
    permissions: 扩展权限,
    hostPermissions: 扩展有权限访问的host,
    installType: 扩展被安装的方式
}

// 获取权限警告
chrome.management.getPermissionWarningsById(exId, function(permissionWarningArray){
    console.log(permissionWarningArray);
});

// exManifest是字符串型的
chrome.management.getPermissionWarningsByManifest(exManifest, function(permissionWarningArray){
    console.log(permissionWarningArray);
});

// 启用或禁用扩展应用
chrome.management.setEnabled(exId, enabled, function(){
    if(enabled){
        console.log('Extension '+exId+' has been enabled.');
    }
    else{
        console.log('Extension '+exId+' has been disabled.');
    }
});

// 卸载扩展
chrome.management.uninstall(exId, {
    // 显示确认窗口
    showConfirmDialog: true
}, function(){
    console.log('Extension '+exId+' has been uninstalled.');
});

chrome.management.uninstallSelf({
    showConfirmDialog: true
}, function(){
    console.log('This extension has been uninstalled.');
});

// 启动应用
chrome.management.launchApp(exId, function(){
    console.log('App '+exId+' has been launched.');
});


// 监听安装、卸载、启用和禁用扩展应用
chrome.management.onInstalled.addListener(function(exInfo){
    console.log('Extension '+exInfo.id+' has been installed.')
});

chrome.management.onUninstalled.addListener(function(exId){
    console.log('Extension '+exId+' has been uninstalled.');
});

chrome.management.onEnabled.addListener(function(exInfo){
    console.log('Extension '+exInfo.id+' has been enabled.');
});

chrome.management.onDisabled.addListener(function(exInfo){
    console.log('Extension '+exInfo.id+' has been disabled.');
});
```


```javascript
// 标签

// 获取到指定id的标签
chrome.tabs.get(tabId, function(tab){
    console.log(tab);
});

// 获取运行的脚本本身所在的标签
chrome.tabs.getCurrent(function(tab){
    console.log(tab);
});

// 获取所有符合指定条件的标签
chrome.tabs.query({
    active: true
}, function(tabArray){
    console.log(tabArray);
});

// query方法可以指定的匹配条件
{
    active: 是否是活动的,
    pinned: 是否被固定,
    highlighted: 是否正被高亮显示,
    currentWindow: 是否在当前窗口,
    lastFocusedWindow: 是否是上一次选中的窗口,
    status: 状态，loading或complete,
    title: 标题,
    url: 所打开的url,
    windowId: 所在窗口的id,
    windowType: 窗口类型，normal、popup、panel或app,
    index: 窗口中的位置
}

// 创建标签
chrome.tabs.create({
    windowId: wId,
    index: 0,
    url: 'http://www.google.com',
    active: true,
    pinned: false,
    openerTabId: tId
}, function(tab){
    console.log(tab);
});

// 复制 指定标签
chrome.tabs.duplicate(tabId, function(tab){
    console.log(tab);
});

// 更新标签
chrome.tabs.update(tabId, {
    url: 'http://www.google.com'
}, function(tab){
   console.log(tab);
});

// 移动标签
chrome.tabs.move(tabIds, {
    'windowId':wId,
    'index':0
}, function(tabs){
    console.log(tabs);
});

// 重载标签
chrome.tabs.reload(tabId, {
    // 是否跳过缓存（强制刷新）
    bypassCache: true
}, function(){
    console.log('The tab has been reloaded.');
});

// 移除标签
chrome.tabs.remove(tabIds, function(){
    console.log('The tabs has been closed.');
});

// 获取当前标签页面的显示语言
chrome.tabs.detectLanguage(tabId, function(lang){
    console.log('The primary language of the tab is '+lang);
});

// 截图(可见部分)
// 只有声明activeTab或<all_url>权限能获取到活动标签的截图
chrome.tabs.captureVisibleTab(windowId, {
    format: 'jpeg',
    quality: 50
}, function(dataUrl){
    window.open(dataUrl, 'tabCapture');
});

// 注入脚本
chrome.tabs.executeScript(tabId, {
    file: 'js/ex.js',
    allFrames: true,

    // 'document_start'、'document_end'或'document_idle'
    runAt: 'document_start'
}, function(resultArray){
    console.log(resultArray);
});

// 注入CSS
chrome.tabs.insertCSS(tabId, {
    file: 'css/insert.css',
    allFrames: false,
    runAt: 'document_start'
}, function(){
    console.log('The css has been inserted.');
});

// 与指定标签中的内容脚本（content script）通信
chrome.tabs.sendMessage(tabId, message, function(response){
    console.log(response);
});

onCreated
onUpdated
onMoved
onActivated
onHighlighted   // 当标签被高亮显示时
onDetached      // 标签脱离窗口时
onAttached      // 标签附着到窗口上时
onRemoved
onReplaced
```

```javascript
// Override Pages, 替换Chrome相应默认的页面
// 只需在Manifest中进行声明即可（一个扩展只能替换一个页面）

"chrome_url_overrides" : {
    "bookmarks": "bookmarks.html"
}

"chrome_url_overrides" : {
    "history": "history.html"
}

"chrome_url_overrides" : {
    "newtab": "newtab.html"
}
```

## 第5章 部分高级API

```javascript
// 下载
chrome.downloads.download(options, callback);
// options
{
    url: 下载文件的url,
    filename: 保存的文件名,

    // "uniquify", "overwrite", "prompt"
    conflictAction: 重名文件的处理方式,
    saveAs: 是否弹出另存为窗口,
    method: 请求方式（POST或GET），
    headers: 自定义header数组,
    body: POST的数据
}
```

```javascript
// 网络请求
// 可以对浏览器向Google发起的网络请求进行更改
// webRequest接口无法在Event Page中使用

// todo
```