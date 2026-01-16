---
title: "微信域名是否被拦截的检测方法（官方跳转接口）"
date: 2022-03-29
slug: wechat-domain-block-check
description: "通过微信官方跳转接口判断域名是否被微信拦截，并结合重定向结果或 Python 正则进行检测的方法说明。"
categories:
  - 工作
tags:
  - 微信
  - 域名检测
  - Python
  - 接口
summary: "通过微信官方跳转接口判断域名是否被微信拦截，并结合重定向结果或 Python 正则进行检测的方法说明。"
aliases:
  - /about-work/303.html
---

微信浏览器官方API：
https://mp.weixinbridge.com/mp/wapredirect?url=https://www.josejang.com

如果域名是被拦截的会重定向到 https://weixin110.qq.com/ ，反之重定向到检测的域名

python 正则判断是否存在：已停止访问该网页

````python
re.findall(r'已停止访问该网页', result)
````



