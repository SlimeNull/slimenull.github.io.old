---
title: '2022年QQ微信内置浏览器 UA 标识, Header 获取'
slug: '2022年QQ微信内置浏览器UA标识,Header获取'
date: 2022-03-01T17:43:03+08:00
tags:
  - 微信
  - QQ
  - 爬虫
  - golang
categories:
  - 灌水
  - note
description: 'UA 标识QQ:Mozilla/5.0 (Linux; Android 11; Redmi Note 8 Pro Build/RP1A.200720.011; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/89.0.4389.72 MQQBrowser/6.2 TBS/045913 Mobile Safari/537.36 V1_AND_SQ_8.8.68_2538_YYB_D A_8086800 QQ/8.8.68.726'
---

## UA 标识

QQ: 

> Mozilla/5.0 (Linux; Android 11; Redmi Note 8 Pro Build/RP1A.200720.011; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/89.0.4389.72 MQQBrowser/6.2 TBS/045913 Mobile Safari/537.36 V1_AND_SQ_8.8.68_2538_YYB_D A_8086800 QQ/8.8.68.7265 NetType/WIFI WebP/0.3.0 Pixel/1080 StatusBarHeight/76 SimpleUISwitch/1 QQTheme/2971 InMagicWin/0 StudyMode/0 CurrentMode/1 CurrentFontScale/1.0 GlobalDensityScale/0.9818182 AppId/537112567 Edg/98.0.4758.102


微信:

> [Mozilla/5.0 (Linux; Android 11; Redmi Note 8 Pro Build/RP1A.200720.011; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/86.0.4240.99 XWEB/3185 MMWEBSDK/20211001 Mobile Safari/537.36 MMWEBID/6210 MicroMessenger/8.0.16.2040(0x2800105F) Process/toolsmp WeChat/arm64 Weixin NetType/WIFI Language/zh_CN ABI/arm64] Edg/98.0.4758.102


## 获取

以上信息是在 2022 年 3 月获取的, 如果软件升级了版本, UA 变更导致这些信心不再有效, 你可以手动获取这些信息


使用 golang 编写一个简单的服务端程序, 监听 8000 端口, 然后把 headers 和 cookies 全部打印出来.


```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(writer http.ResponseWriter, request *http.Request) {
		writer.Write([]byte("========== Headers ==========\n"))
		for k, v := range request.Header {
			writer.Write([]byte(fmt.Sprintf("%s: %s;\n", k, v)))
		}
		writer.Write([]byte("========== Cookies ==========\n"))
		for _, v := range request.Cookies() {
			writer.Write([]byte(fmt.Sprintf("%s - %s: %s;\n", v.Domain, v.Name, v.Value)))
		}
	})
	http.ListenAndServe(":8000", nil)
}
```


手机连接到电脑所在局域网, 然后使用QQ微信访问电脑的 8000 端口即可看到 Headers 和 Cookies 了.
