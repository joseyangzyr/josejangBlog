---
title: "WordPress 外部链接跳转的实现方式"
date: 2021-06-25
slug: wp-external-link-redirect
description: "记录一种在 WordPress 中实现外部链接跳转与参数校验的方法，并给出 Hugo 静态博客下的等价实现方案。"
categories:
  - 工作
summary: "记录一种在 WordPress 中实现外部链接跳转与参数校验的方法，并给出 Hugo 静态博客下的等价实现方案。"
aliases:
  - /about-work/154.html
---

在早期使用 WordPress 时，为了控制外部链接，常见做法是：

- 统一外链出口
- 防止权重直接流向外站
- 防止恶意参数注入

于是我当时写了一个 `go/index.php` 来处理外链跳转。

---

## 第一步

新建文/go/index.php并加入一下内容：

````php
<?php
    //$t_url=$_GET['url']; //此代码无法支持带请求参数的目的地址，已弃用！
    $t_url = preg_replace('/^url=(.*)$/i','$1',$_SERVER["QUERY_STRING"]); //这个支持
    if(!empty($t_url)) {
        preg_match('/(http|https):\/\//',$t_url,$matches);
    if($matches){
        $url=$t_url;
        $title='页面加载中,请稍候...';
    } else {
        preg_match('/\./i',$t_url,$matche);
        if($matche){
            $url='http://'.$t_url;
            $title='页面加载中,请稍候...';
        } else {
            $url='https://www.josejang.com/';
            $title='参数错误，正在返回首页...';
        }
    }
    } else {
        $title='参数缺失，正在返回首页...';
        $url='https://www.josejang.com/';
    }
    ?>
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <meta http-equiv="refresh" content="1;url='<?php echo $url;?>';">
    <title><?php echo $title;?></title>
    <style>
    body{background:#000}.loading{-webkit-animation:fadein 2s;-moz-animation:fadein 2s;-o-animation:fadein 2s;animation:fadein 2s}@-moz-keyframes fadein{from{opacity:0}to{opacity:1}}@-webkit-keyframes fadein{from{opacity:0}to{opacity:1}}@-o-keyframes fadein{from{opacity:0}to{opacity:1}}@keyframes fadein{from{opacity:0}to{opacity:1}}.spinner-wrapper{position:absolute;top:0;left:0;z-index:300;height:100%;min-width:100%;min-height:100%;background:rgba(255,255,255,0.93)}.spinner-text{position:absolute;top:50%;left:50%;margin-left:-90px;margin-top: 2px;color:#BBB;letter-spacing:1px;font-weight:700;font-size:36px;font-family:Arial}.spinner{position:absolute;top:50%;left:50%;display:block;margin-left:-160px;width:1px;height:1px;border:25px solid rgba(100,100,100,0.2);-webkit-border-radius:50px;-moz-border-radius:50px;border-radius:50px;border-left-color:transparent;border-right-color:transparent;-webkit-animation:spin 1.5s infinite;-moz-animation:spin 1.5s infinite;animation:spin 1.5s infinite}@-webkit-keyframes spin{0%,100%{-webkit-transform:rotate(0deg) scale(1)}50%{-webkit-transform:rotate(720deg) scale(0.6)}}@-moz-keyframes spin{0%,100%{-moz-transform:rotate(0deg) scale(1)}50%{-moz-transform:rotate(720deg) scale(0.6)}}@-o-keyframes spin{0%,100%{-o-transform:rotate(0deg) scale(1)}50%{-o-transform:rotate(720deg) scale(0.6)}}@keyframes spin{0%,100%{transform:rotate(0deg) scale(1)}50%{transform:rotate(720deg) scale(0.6)}}
    </style>
    </head>
    <body>
    <div class="loading">
      <div class="spinner-wrapper">
        <span class="spinner-text">页面加载中,请稍候...</span>
        <span class="spinner"></span>
      </div>
    </div>
    </body>
    </html>
````

## 第二步

在主题functions.php文件中加入：

````php
//给外部链接加上跳转
    add_filter('the_content','the_content_nofollow',999);
    function the_content_nofollow($content)
    {
        preg_match_all('/<a(.*?)href="(.*?)"(.*?)>/',$content,$matches);
        if($matches){
            foreach($matches[2] as $val){
                if(strpos($val,'://')!==false && strpos($val,home_url())===false && !preg_match('/\.(jpg|jepg|png|ico|bmp|gif|tiff)/i',$val)){
                    $content=str_replace("href=\"$val\"", "href=\"".home_url()."/go/?url=$val\" ",$content);
                }
            }
        }
        return $content;
    }
````

