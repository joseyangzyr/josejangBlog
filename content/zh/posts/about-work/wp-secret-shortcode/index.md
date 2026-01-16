---
title: "WordPress 输入密码显示隐藏内容的实现方式"
date: 2021-06-23
slug: wp-secret-content
description: "记录一种在 WordPress 中通过 shortcode 实现输入密码显示隐藏内容的方法，并给出 Hugo 静态博客的等价实现方案。"
categories:
  - 工作
summary: "记录一种在 WordPress 中通过 shortcode 实现输入密码显示隐藏内容的方法，并给出 Hugo 静态博客的等价实现方案。"
aliases:
  - /about-work/142.html
  - /about-work/wp-secret-content.html
---

在 WordPress 中，有时会遇到这样的需求：

> **文章中某一部分内容，需要用户输入密码后才能查看。**

下面记录一种当年在 WordPress 中使用的实现方式，并给出在 Hugo 中的等价方案。

---

## WordPress 实现思路

将下面代码加入 `functions.php`：

```php
function e_secret($atts, $content=null){
    extract(shortcode_atts(array('key'=>null), $atts));
    if(isset($_POST['e_secret_key']) && $_POST['e_secret_key']===$key){
        return '<div class="e-secret">'.$content.'</div>';
    } else {
        return '<form class="e-secret" method="post">
            <label>输入密码查看加密内容：</label>
            <input type="password" name="e_secret_key">
            <input type="submit" value="确定">
        </form>';
    }
}
add_shortcode('secret','e_secret');
```

在发布文章时使用：

````php
[secret key="这里设置密码"]
	这里是隐藏内容
[/secret]
````


例子,密码为:josejang
[secret key="josejang"]
这里是需要隐藏的内容内容
[/secret]

