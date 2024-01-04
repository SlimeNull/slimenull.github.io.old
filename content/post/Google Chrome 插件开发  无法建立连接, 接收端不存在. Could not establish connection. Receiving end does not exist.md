---
title: 'Google Chrome 插件开发: 无法建立连接, 接收端不存在. Could not establish connection. Receiving end does not exist'
slug: 'GoogleChrome插件开发 无法建立连接,接收端不存在.Couldnotestablishconnection.Receivingenddoesnotexist'
date: 2022-10-14T10:00:19+08:00
tags:
  - chrome
  - 前端
  - javascript
categories:
  - note
  - 成长记录
description: 'Google Chrome 插件开发: 无法建立连接, 接收端不存在. Could not establish connection. Receiving end does not exist'
---

## 通过以下代码向当前页面发送 "start" 消息:

```js
chrome.tabs.query({active: true,currentWindow: true}, tabs => {
    let tab = tabs[0];
    chrome.tabs.sendMessage(tab.id, "start");
});
```

## 报错:

```
Uncaught (in promise) Error: Could not establish connection. Receiving end does not exist.
```

## 可能的原因:


接收端, 也就是说目标页面必须有 chrome.runtime.onMessage 监听消息, 如果 "content-script" 没有注入到页面中, 那么这个页面就无法接收消息


如果你的插件刚刚加载, 并且在一个已经加载完毕的页面中使用它, 则会出这个问题.


因为这个页面已经加载完了, 它并没有被注入脚本, 你需要刷新页面, 使脚本注入到页面中, 然后才可以发送消息


## 平台不允许文章内容太少, 下面是水

sendMessage
chrome.tabs.sendMessage(integer tabId, any message, function responseCallback)
向指定标签页中的内容脚本发送一个消息，当发回响应时执行一个可选的回调函数。当前扩展程序在指定标签页中的每一个内容脚本都会收到 runtime.onMessage 事件。


| 参数 | 类型 |
| --- | --- |
| tabId | integer |
| message | any |
| responseCallback | optional function |


如果您指定了 responseCallback 参数，它应该指定一个如下形式的函数：


function(any response) {...};
response ( any )
请求处理程序发出的 JSON 响应对象。如果连接到指定标签页的过程中发生错误，将不传递参数调用回调函数，并将 runtime.lastError 设置为错误消息。
