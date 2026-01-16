---
title: "Nginx 判断搜索引擎蜘蛛并进行跳转的配置示例"
date: 2020-02-17
slug: nginx-spider-redirect
description: "记录一段通过 Nginx 判断搜索引擎蜘蛛 User-Agent，仅允许蜘蛛访问真实页面，其他访问进行跳转的配置示例。"
categories:
  - 工作
summary: "记录一段通过 Nginx 判断搜索引擎蜘蛛 User-Agent，仅允许蜘蛛访问真实页面，其他访问进行跳转的配置示例。"
aliases:
  - /about-work/95.html
  - /about-work/nginx-spider-redirect.html
---

在某些 SEO 或测试场景下，可能会有这样的需求：

> **搜索引擎蜘蛛正常访问页面，普通用户访问时跳转到指定地址。**

下面记录一段当时实际使用过的 **Nginx 判断蜘蛛并跳转的配置示例**。

---

## 核心思路

- 通过 `$http_user_agent` 判断访问者
- 如果是搜索引擎蜘蛛 → 正常访问
- 如果不是蜘蛛 → 统一跳转到指定页面

---

## Nginx 配置示例

````
location / {
	if  ($http_user_agent ~* "Baiduspider|Baiduspider-render|360Spider|bingbot|Googlebot|Googlebot-Image|Yahoo! Slurp China|Yahoo! Slurp|iaskspider|Sogou web spider|Sogou Push Spider|Sogou Pic Spider|YisouSpider|Sosospider|YoudaoBot") {
		 set $Spider flag;
	  }
	if ($Spider != flag){
		rewrite ".*" https://www.josejang.com; 
	}
	if ($Spider = flag){
		rewrite ^/([^@]+)$ /index.php?$1 last;
	}
}
````

