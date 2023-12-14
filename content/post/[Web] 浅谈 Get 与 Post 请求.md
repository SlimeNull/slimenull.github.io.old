---
title: '[Web] 浅谈 Get 与 Post 请求'
slug: '[Web]浅谈Get与Post请求'
date: 2021-01-05 17:32:52
tags:
  - html5
  - web
  - python
categories:
  - 网络
  - 笔记
  - 成长记录
description: 'Get 与 Post 请求HTTP请求:前端与后端的数据传递是通过 HTTP 请求实现的, 浏览器(前端)向服务器(后端)发送一个 HTTP 请求, 后端对请求进行处理, 然后再给浏览器发一个响应, 这就是 HTTP 的基本概念.get 和 post 是HTTP请求的两种方式, 最明显的区别是传递参数.如果你不大了解 HTTP 请求, 可以先查看文章末对 HTTP 的简述.Get:关于 Get 请求get 一般用来获取数据, 因为其本意就是获取. 浏览器访问一个页面时, 发送的第一'
---

## Get 与 Post 请求


#### HTTP请求:


> 前端与后端的数据传递是通过 HTTP 请求实现的, 浏览器(前端)向服务器(后端)发送一个 HTTP 请求, 后端对请求进行处理, 然后再给浏览器发一个响应, 这就是 HTTP 的基本概念.
>
> get 和 post 是HTTP请求的两种方式, 最明显的区别是传递参数.
>
> 如果你不大了解 HTTP 请求, 可以先查看文章末对 HTTP 的简述.


#### Get:


- 关于 Get 请求
  - get 一般用来获取数据, 因为其本意就是获取. 浏览器访问一个页面时, 发送的第一个请求就是 Get.
  - get 的参数将直接写明在请求 url 中, 这意味着, 任何人可以直接通过浏览器上方的地址来清除的看到, 例如你要下载一个东西, 文件名叫 'idea', 将文件名作为filename参数传递过去, 那么这个请求类似于这样:

     ```
     https://xxx.com/download/?filename=idea
     ```

  3. 在url中, 写一个''?'表示后面的内容是参数, 然后以 参数名=参数值 的格式表示参数, 如果要传多个参数, 则使用 '&' 连接它们

  4. 服务端接收到你的请求后, 可以根据参数来做出对应处理, 然后发送合适的响应.

- 一个例子:

  - 在浏览器新建选项卡, 在url中输入一个地址, 发送的是一个get请求, 所以我们可以直接通过这个来测试.

  - hitokoto 是一个提供'一句话'的网站, 提供了接口以供开发这调用, 其中一个是:

     | 地址           | 协议  | 方法 |
     | -------------- | ----- | ---- |
     | v1.hitokoto.cn | HTTPS | ANY  |

     其中一个参数是:

     | 参数   | 值               | 可选 | 说明       |
     | ------ | ---------------- | ---- | ---------- |
     | encode | text / json / js | 是   | 返回的编码 |
     
     

     方法是Any, 即, 同时支持 get 与 post, 那么我们尝试使它返回一个格式为文本的结果, 对应的 url 是:
     
     ```
     https://v1.hitokoto.cn/?encode=text
     ```
     
     hitokoto 服务器返回了我们所需要的值:
     
     
![hitokoto返回的纯文本数据](images/20210105165205507.png)
     
     那么, 如果是json呢? 试试看吧:
     
     ```
     https://v1.hitokoto.cn/?encode=json
     ```
     
     hitokoto 服务器又返回了我们所需要的值:
     
     
![hitokoto返回的json数据](images/20210105165331960.png)


#### Post:


- 关于 Post 请求

  - Post 有 '邮递,布置' 的意思, 一般用来将数据提交给服务端.
  - 看完 Get, 你也知道了 Get 的一个特点: 参数直接暴露在 url 中, 直接被别人看到, 肯定是不大妥当的, 假如你要向服务器提交你的密码, 让别人看到, 那就不好了, 而 Post 相对 Get 来说, 就稍微安全点了, 因为参数在这个HTTP请求体中.
  <br/>
  
  一个POST请求的概述:

![一个POST请求的概述:](images/20210110114706246.png)
它的响应内容:
![一个POST请求的响应内容](images/20210110114759960.png)
由此可知, POST可以大致认为, 只是将参数放到了请求体中, 相对来说, 严谨一些.

- 一个例子:

  因为大多数浏览器不能直接发送 post 请求, 所以我们通过 python 与一个测试网页来测试:

  - 有道有一个翻译接口示例, 支持 post 请求

    地址: 'https://aidemo.youdao.com/trans'

    | 参数 | 介绍           | 值                        |
    | ---- | -------------- | ------------------------- |
    | q    | 翻译文本       | 要进行翻译的文本          |
    | from | 从哪种语言翻译 | auto / zh-CHS / en / 其他 |
    | to   | 翻译到哪种语言 | auto / zh-CHS / en / 其他 |

    - Python:

     1. Python 可以通过 requests 包来发送 post 请求.

     2. 编写代码:

        ```python
        import requests
        
        url = "https://aidemo.youdao.com/trans"
        args = {"q": "翻译这段话", "from": "auto", "to": "auto"}
        
        resp = requests.post(url, args)  # 发送请求, 并将响应赋值到resp
        print(resp.text)                 # 打印响应解码后的文本
        ```

     3. 运行后, 可以看到, 返回了我们需要的数据:

        ![python发送post请求](images/20210105165515493.png)


    - 编写一个简单的网页来测试:

     1. 在HTML中, 可以通过 form 表单, 可以发送 get 和 post 请求.
     
     2. 编写代码:
     
        ```html
        <!DOCTYPE html>
        <html>
            <head>
                <title>Post Request Demo</title>
            </head>
            <body>
                <form action="https://aidemo.youdao.com/trans" method="post" target="_blank">
                    <label for="kwd">关键字:</label>
                    <input id="kwd" type="text" name="q" placeholder="输入关键词"/>
                    <input type="hidden" name="from" value="auto"/>
                    <input type="hidden" name="to" value="auto"/>
                    <input type="submit" value="请求"/>
                </form>
            </body>
        </html>
        ```
     
     3. 效果:
     
        
![post请求测试demo](images/20210105165549904.png)

     
     4. 输入 "白嫖有道翻译API真棒" 并按下请求按钮:
     
        返回了我们所需要的数据:
     
        
![在这里插入图片描述](images/20210105165649711.png)

#### 指正:

> 可能会有人告诉你, get 比 post 安全, 但事实上, 这仅仅是针对门外汉, 你只需要打开浏览器调试功能, 就可以看到这个页面的一切请求以及请求的各种详细内容, post 的参数也能看到.
> 所以说, 说 post 安全, 只是指它的参数不能直接在 url 中看到, 但只要你想看, 只需要打开调试...




### HTTP 概述:

> 一个HTTP数据包有头和体两部分, 头中包含了一些摘要数据, 这些数据以键值对的形式存放, 例如指定可接收的数据类型, 指定编码, 压缩算法, 数据长度等. 体中是这个包的主要内容, 例如传输文件时, 文件内容就位于体中. 如果想要详细的了解HTTP数据的内容, 不妨在浏览器中按下F12, 打开调试, 进入 '网络(Network)' 选项卡, 刷新页面, 然后观察一个个的数据包.
