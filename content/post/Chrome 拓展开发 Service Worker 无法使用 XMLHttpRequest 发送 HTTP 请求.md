---
title: 'Chrome 拓展开发 Service Worker 无法使用 XMLHttpRequest 发送 HTTP 请求'
slug: 'Chrome拓展开发ServiceWorker无法使用XMLHttpRequest发送HTTP请求'
date: 2022-10-15 11:17:04
tags:
  - chrome
  - http
  - javascript
categories:
  - 成长记录
  - 灌水
description: 'Chrome 拓展开发 Servcie Worker 无法使用 XMLHttpRequest, 应该使用 fetch 替代'
---

相关的报错信息:
XMLHttpRequest is not defined at chrome-extension:


## 原因

显然, Service Worker 不支持 XMLHttpRequest


## 解决方案

用 fetch


## 相关资料

![](images/8dc188509a7f4756bf88833ae8796085.png)
通过 fetch 上传 JSON 数据

```js
const data = { username: 'example' };    // 定义数据

fetch('https://example.com/profile', {
  method: 'POST', // or 'PUT'            // 指定请求方法
  headers: {
    'Content-Type': 'application/json',  // 指定内容类型
  },
  body: JSON.stringify(data),            // 传入内容
})
  .then((response) => response.json())   // 取响应 json
  .then((data) => {                      // 对解析完成的 json 进行操作
    console.log('Success:', data);
  })
  .catch((error) => {                    // 捕捉错误
    console.error('Error:', error);
  });
```

或者最简单的, 仅仅上传, 不考虑响应与异常情况

```js
const data = { username: 'example' };    // 定义数据

fetch('https://example.com/profile', {
  method: 'POST', // or 'PUT'            // 指定请求方法
  headers: {
    'Content-Type': 'application/json',  // 指定内容类型
  },
  body: JSON.stringify(data),            // 传入内容
});
```
