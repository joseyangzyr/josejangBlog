---
title: "WordPress 清理编辑器遗留痕迹"
date: 2026-01-04T14:43:00+08:00
slug: "wordpress-clean-editor-artifacts"
description: "分析 Notion / AI 编辑器复制粘贴到 WordPress 后产生的 data-start、data-end 等无意义属性问题，并给出安全、可控的清理方案。"
summary: "分析 Notion / AI 编辑器复制粘贴到 WordPress 后产生的 data-start、data-end 等无意义属性问题，并给出安全、可控的清理方案。"
categories:
  - 工作
tags:
  - WordPress
  - 编辑器
  - Notion
  - HTML清理
  - 内容维护
aliases:
  - /about-work/375.html
---

````html
<p data-start="1724" data-end="1795">Most “bad coffee” problems...</p>
<p data-start="1725" data-end="1796">This is why French press...</p>
<p data-start="1726" data-end="1797"><img ... /></p>
````

这是 Notion / AI 编辑器导出的痕迹，WordPress 不需要。

## 问题：Notion / AI 编辑器 / 复制粘贴

留下 data-start / data-end 这种无意义属性

它们对：

SEO ❌

- 渲染 ❌
- 维护 ❌
  全都没价值

## 解决方案：最安全、可控的 WordPress 清理方案

把下面代码加到 functions.php：

````php
/**
 * Remove editor leftover attributes like data-start / data-end
 * Only affects frontend output
 */
function josejang_clean_editor_artifacts($content) {
    // Remove data-start / data-end attributes
    $content = preg_replace('/\sdata-(start|end)="\d+"/i', '', $content);

    return $content;
}
add_filter('the_content', 'josejang_clean_editor_artifacts', 20);
````



