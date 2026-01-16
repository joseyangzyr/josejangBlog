---
title: "WordPress REST API 最细接口详解"
date: 2021-12-28
slug: wordpress-rest-api-detail
description: "详细整理 WordPress REST API 接口用法，包含用户注册、登录、Token 获取、接口权限说明与示例代码，适合二次开发与接口对接。"
categories:
  - 工作
summary: "详细整理 WordPress REST API 接口用法，包含用户注册、登录、Token 获取、接口权限说明与示例代码，适合二次开发与接口对接。"
aliases:
  - /about-work/283.html
---

WordPress Rest API 最细接口详解
注：
1、以下接口拼接自己的域名，如：https://www.josejang.com/
2、在wpAPI文档中，有些接口没有提供，建站时需要后台服务器帮助提供，以下我这边是自己后台服务器提供接口用米字号作*标记。
3、以下接口get请求一般请求头可以不带token。
4、wo记得没错的话，wp的token有效期为24小时。
————————————————
1、登录注册模块
1.1、用户注册 *
请求协议|请求方法：HTTP|POST
接口路径：/fapi/userregster.php
请求参数：

参数名	说明	类型
username	用户名，必填	[string]
password	密码，必填	[string]
email	邮箱	[string]
nickname	昵称	[string]
phone	手机号	[string]
成功示例：

````php
24
````

直接返回新用户id。

1.2、获取Token
请求协议|请求方法：HTTP|POST

接口路径：/wp-json/jwt-auth/v1/token
请求参数
参数类型：表单

参数名	说明	必填	类型
username	用户名	是	[string]
password	密码	是	[string]
成功示例：

````php
{
    "token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC96aG91YnV5aS5tZWlsYXNoaWRhaS5jbiIsImlhdCI6MTU4Njc2NDA3MCwibmJmIjoxNTg2NzY0MDcwLCJleHAiOjE1ODczNjg4NzAsImRhdGEiOnsidXNlciI6eyJpZCI6IjIyIn19fQ.7kgtWfUTlm6tbZX63V7OEpo08PAxakZiK4e9DE6Nvb8",
    "user_email":"",
    "user_nicename":"100022",
    "user_display_name":"1111111"
}
````

1.3、验证Token
请求协议|请求方法：HTTP|POST

接口路径：/wp-json/jwt-auth/v1/token/validate
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
成功示例：

````php
{
    "code":"jwt_auth_valid_token",
    "data":{
        "status":200
    }
}
````

注意点1： 获取的token要放在请求头，并且token值的格式必须为：Bearer+token值，否侧无法判断。
注意点2： 要实现这个接口，需要后台服务器授权验证，详细参考：使用 JWT Authentication 验证 WordPress REST API 权限，只有授权验证处理通过，这个接口才能请求成功，否则会返回错误，要求授权。

1.4、账户登录 *
请求协议|请求方法：HTTP|POST

接口路径：/fapi/loginwithtoken.php

请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数：

参数名	说明	必填	类型
username	用户名	是	[string]
pwd	密码	是	[string]
成功示例：

````php
{
    "data":{
        "ID":"22",
        "user_login":"1111111",
        "user_pass":"$P$BSz.lD0LqQxK/v8E9Un1Cc3VwnURpp.",
        "user_nicename":"100022",
        "user_email":"wyp001@123.com",
        "user_url":"",
        "user_registered":"2020-04-13 07:23:02",
        "user_activation_key":"",
        "user_status":"0",
        "display_name":"1111111",
        "father_id":"0",
        "reg_ip":""
    },
    "ID":22,
    "caps":{
        "author":true
    },
    "cap_key":"wp_capabilities",
    "roles":[
        "author"
    ],
    "allcaps":{
        "upload_files":true,
        "edit_posts":true,
        "edit_published_posts":true,
        "publish_posts":true,
        "read":true,
        "level_2":true,
        "level_1":true,
        "level_0":true,
        "delete_posts":true,
        "delete_published_posts":true,
        "author":true
    },
    "filter":null
}
````

1.5、获取用户信息
请求协议|请求方法：HTTP|GET

接口路径：/wp-json/wp/v2/users/me

请求头部：

头部标签	必填	头部内容
Authorization	是	用户token，格式：Bearer+token
成功示例：

````php
{
    "id":22,
    "name":"1111111",
    "url":"",
    "description":"描述啊描述",
    "link":"http://josejang.com/author/100022/",
    "slug":"100022",
    "avatar_urls":{
        "24":"http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=24&d=mm&r=g",
        "48":"http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=48&d=mm&r=g",
        "96":"http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=96&d=mm&r=g"
    },
    "meta":[

    ],
    "woocommerce_meta":{
        "activity_panel_inbox_last_read":"",
        "activity_panel_reviews_last_read":"",
        "categories_report_columns":"",
        "coupons_report_columns":"",
        "customers_report_columns":"",
        "orders_report_columns":"",
        "products_report_columns":"",
        "revenue_report_columns":"",
        "taxes_report_columns":"",
        "variations_report_columns":"",
        "dashboard_sections":"",
        "dashboard_chart_type":"",
        "dashboard_chart_interval":"",
        "dashboard_leaderboard_rows":""
    },
    "_links":{
        "self":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/users/22"
            }
        ],
        "collection":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/users"
            }
        ]
    }
}
````

1.6、修改用户信息
wpAPI中没有找到该接口，是根据获取用户信息get方法，推理出来，修改用户信息为post，测试部分字段有效，以下请求参数列表，是个人认为会有用到，才罗列出来的。
请求协议|请求方法：HTTP|POST
接口路径：/wp-json/wp/v2/users/me
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数

参数名	说明	必填	类型
name	显示名称	是	[string]
first_name	名字	是	[string]
last_name	姓氏	是	[string]
email	邮箱	是	[string]
locale	语言设置，默认：zh_CN	是	[string]
nickname	昵称	是	[string]
roles	如：[ “author” ]	是	[array]
capabilities	功能分类设置，查看返回结果	是	[string]
avatar_urls	头像，如：{ “24”:“http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=24&d=mm&r=g”, “48”:“http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=48&d=mm&r=g”, “96”:“http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=96&d=mm&r=g” }。	是	[object]
woocommerce_meta	店铺的相关配置	是	[string]
成功示例：

````php
{
    "id":22,
    "username":"1111111",
    "name":"测试将name1111111修改ceshi",
    "first_name":"",
    "last_name":"",
    "email":"wyp001@123.com",
    "url":"",
    "description":"描述啊描述",
    "link":"http://josejang.com/author/100022/",
    "locale":"zh_CN",
    "nickname":"1111111",
    "slug":"100022",
    "roles":[
        "author"
    ],
    "registered_date":"2020-04-13T07:23:02+00:00",
    "capabilities":{
        "upload_files":true,
        "edit_posts":true,
        "edit_published_posts":true,
        "publish_posts":true,
        "read":true,
        "level_2":true,
        "level_1":true,
        "level_0":true,
        "delete_posts":true,
        "delete_published_posts":true,
        "author":true
    },
    "extra_capabilities":{
        "author":true
    },
    "avatar_urls":{
        "24":"http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=24&d=mm&r=g",
        "48":"http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=48&d=mm&r=g",
        "96":"http://cn.gravatar.com/avatar/cf29dfa8761d26cf525bd3275293a527?s=96&d=mm&r=g"
    },
    "meta":[

    ],
    "woocommerce_meta":{
        "activity_panel_inbox_last_read":"",
        "activity_panel_reviews_last_read":"",
        "categories_report_columns":"",
        "coupons_report_columns":"",
        "customers_report_columns":"",
        "orders_report_columns":"",
        "products_report_columns":"",
        "revenue_report_columns":"",
        "taxes_report_columns":"",
        "variations_report_columns":"",
        "dashboard_sections":"",
        "dashboard_chart_type":"",
        "dashboard_chart_interval":"",
        "dashboard_leaderboard_rows":""
    },
    "_links":{
        "self":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/users/22"
            }
        ],
        "collection":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/users"
            }
        ]
    }
}
````

1.7、获取所有的用户信息
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/users
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
1.8、获取指定用户ID的用户信息
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/users/用户id，如：1
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
2、页面
2.1、获取所有的页面信息
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/pages
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
2.2、获取指定页面ID的页面信息
请求协议|请求方法：HTTP|POST
接口路径：/wp-json/wp/v2/pages/289
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
3、文章的分类和标签
3.1、获取文章分类数组
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/categories
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
成功示例：

````php
[
    {
        "id":14,
        "count":13,
        "description":"",
        "link":"http://josejang.com/category/%e5%85%ab%e5%8d%a6%e5%8d%a6%e8%b1%a1/",
        "name":"八卦卦象",
        "slug":"%e5%85%ab%e5%8d%a6%e5%8d%a6%e8%b1%a1",
        "taxonomy":"category",
        "parent":0,
        "meta":[

        ],
        "_links":{
            "self":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/categories/14"
                }
            ],
            "collection":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/categories"
                }
            ],
            "about":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/taxonomies/category"
                }
            ],
            "wp:post_type":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/posts?categories=14"
                }
            ],
            "curies":[
                {
                    "name":"wp",
                    "href":"https://api.w.org/{rel}",
                    "templated":true
                }
            ]
        }
    },
]
````

3.2、获取指定分类ID的分类信息
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/categories/分类id
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
3.3、获取文章的所有标签
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/tags
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
3.4、获取指定标签ID的标签信息
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/tags/标签id
接口使用状态：正常启用
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
3.5、获取当前wp所有的内容类型
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/types
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
3.6、获取指定内容类型
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/types/(post|page|attachment 三种类型)
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
4、文章
核心模块

4.1、获取文章列表
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/posts
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数：

参数名	说明	必填	类型
page	指定要返回的结果的页面	是	[string]
per_page	返回的记录数，指定为1到100之间的整数	是	[string]
offset	指定开始检索帖子的任意偏移量.offset = 6将使用每页的默认帖子数，但从集合中的第6个帖子开始	是	[string]
order	默认降序。order = asc（升序）、order = desc（降序）。	是	[string]
orderby	控制集合被排序的字段，默认为orderby = date。有效值为“date”，“relevance”，“id”，“include”，“title”和“slug” 有关其他集合支持的值，请参阅REST API参考	是	[string]
成功示例：

````php
[
    {
        "id":165,
        "date":"2020-03-10T16:06:43",
        "date_gmt":"2020-03-10T08:06:43",
        "guid":{
            "rendered":"/?p=165"
        },
        "modified":"2020-03-10T16:07:10",
        "modified_gmt":"2020-03-10T08:07:10",
        "slug":"%e5%85%ad%e7%88%bb%e7%ad%91%e5%9f%ba%e7%ac%ac%e4%b8%80%e5%85%b3-%e5%85%ab%e5%8d%a6%e5%8d%a6%e8%b1%a1%e4%b9%8b%e5%85%91%e5%ae%ab%e5%85%ab%e5%8d%a6",
        "status":"publish",
        "type":"post",
        "link":"/2020/03/10/%e5%85%ad%e7%88%bb%e7%ad%91%e5%9f%ba%e7%ac%ac%e4%b8%80%e5%85%b3-%e5%85%ab%e5%8d%a6%e5%8d%a6%e8%b1%a1%e4%b9%8b%e5%85%91%e5%ae%ab%e5%85%ab%e5%8d%a6/",
        "title":{
            "rendered":"六爻筑基第一关——八卦卦象之兑宫八卦"
        },
        "content":{
            "rendered":" <p><strong>一、</strong>兑为泽<strong></strong></p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-62.png" alt="" class="wp-image-166" srcset="http://josejang.com/wp-content/uploads/2020/03/image-62.png 299w, http://josejang.com/wp-content/uploads/2020/03/image-62-150x150.png 150w" sizes="(max-width: 299px) 100vw, 299px" /></figure> <p></p> <p>1）从卦本身的组合来看：</p> <p>內外皆是兑，兑为泽，为口，两口相对，意思是在交谈，或者是在争吵，与人交流，可以得益，但说多了又有言过其实之嫌。 2）用六爻的象来看：</p> <p>&nbsp;&nbsp;&nbsp;&nbsp;这个卦，世在六爻，主头部，脸面， 生五爻之金，金主声音，也是在谈论，讲话的意思。从六爻组合的角度来看，此卦，一般不宜女子预测婚姻，虽然官鬼生世，但世在六爻，官鬼在初爻，六爻为天，初爻为地，两人各居一方，分居两地之象。 若是求财， 财旺为佳，可以有大财得， 而且多是凭借三寸不烂之舌，得人钱财， 因为世为财库，财被我把持，兑为泽，为说的意思。若是男的预测婚姻， 财入二爻宅爻，妻虽然入了正位， 但兑为缺，家庭不能美满，会有许多遗憾，常常争吵不断，尤其是怕初爻，二爻同时发动，则妻子多有外遇。 3）卦象含义：</p> <p>这个卦包含有争吵，口舌，欠缺，不完美，破损，喜悦等意思。 二、泽水困　　</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-63.png" alt="" class="wp-image-167" srcset="http://josejang.com/wp-content/uploads/2020/03/image-63.png 306w, http://josejang.com/wp-content/uploads/2020/03/image-63-300x286.png 300w" sizes="(max-width: 306px) 100vw, 306px" /></figure> <p>1）从卦本身的组合来看：</p> <p>外卦为兑，为缺，內卦为坎，为水，泽中无水，水漏泽枯，处于困境。所以，名之为困。 2）用六爻的象来看：</p> <p>世在初爻，初爻为脚，主行走， 财持世，财主饮食，就是得到食物，在路上吃喝东西。但是，此卦六合，合则为留，为绊， 合世者为亥水，是被水所困。 古人说，困者，危也，没有水喝，自然为凶。 这个卦，财爻持世，又是六合卦，六爻预测一般为吉，但世在初爻，爻位比较低，很难发展到高层次。 这个卦，兄弟与二爻相合，多有朋友到家。 3）卦象含义：</p> <p>这个卦包含有被困，阻力，尽头，缓慢，迟滞，无法施展等意思。 三、泽地萃</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-64.png" alt="" class="wp-image-168" srcset="http://josejang.com/wp-content/uploads/2020/03/image-64.png 331w, http://josejang.com/wp-content/uploads/2020/03/image-64-300x261.png 300w" sizes="(max-width: 331px) 100vw, 331px" /></figure> <p>1）按卦本身的象来看：</p> <p>外卦为泽，为口，为水，內卦为坤，为地，合起来，就是地面上开口，有坑，水在地上聚成池塘，所以名之为萃。 古人说，萃者，聚也。 2）按六爻分爻来看：</p> <p>此卦为天地一统，天地合一之象。就是把宇宙大爆炸以后弥散的东西重新再聚到一起。 大家看看此卦的特点，六爻和初爻都是父母未土， 从六爻的爻位上来看，六爻为天，初爻为地，而父母本身也是天地的意思。 所以，六爻的父母未土就是天，初爻的父母未土就是地。大家看，六爻父母未土，从上往下看，是连续相生的， 六爻生五爻， 五爻生四爻， 四爻生三爻，三爻生二爻，二爻生初爻，父母未土又原来的天变成了地， 这就是天地相会， 回到了天地没有分开的宇宙状态。而这个过程必须要经过世爻才能完成。 世爻为一个卦的主体，天地会天下所有的东西都通过各种方式生到了我这里， 就等于自己拥有了天下的东西，众所归一，完全就是聚的意思。六十四卦象，这样的组合，只有这个卦。&nbsp;3）卦象含义：</p> <p>这个卦有汇聚，汇合，归属，集中，同意，和合，完美，收拾，归纳，团结等意思。 四、泽山咸</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-65.png" alt="" class="wp-image-169" srcset="http://josejang.com/wp-content/uploads/2020/03/image-65.png 426w, http://josejang.com/wp-content/uploads/2020/03/image-65-300x211.png 300w" sizes="(max-width: 426px) 100vw, 426px" /></figure> <p>1）从卦本身的组合来看：</p> <p>上卦为兑，为少女，下卦为艮，为少男， 阳下为刚，性向上， 兑上为阴，为柔，性向下， 阴阳正配，虽然在內外，但心通一处，为感应而通，所谓心有灵犀一点通， 古人说，咸者，感也，就是这个意思。</p> <p>2）用六爻的象来看：</p> <p>世在三爻，三爻为门，立足于通道，进出口。六爻为天，父母为信息，初爻为地，临父母也为信息， 天地信息都生我，就是与天地相通，可以感应到宇宙的所有信息。</p> <p>3）卦象含义：</p> <p>这个卦包含有感应，感觉，体会，理解，沟通，联系等意思。 若求财，男子预测，财旺则喜应动生世，因为应为财库，就是别人挣到钱，积攒起来拿到我这里来。 若是预测婚姻， 则不可， 男多找二婚，或是不是处女，或是女朋友到别人怀抱里去了。女的预测男朋友，不是有外遇，就是对方为有老婆的人。</p> <p>五、水山蹇</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-66.png" alt="" class="wp-image-170" srcset="http://josejang.com/wp-content/uploads/2020/03/image-66.png 406w, http://josejang.com/wp-content/uploads/2020/03/image-66-300x219.png 300w" sizes="(max-width: 406px) 100vw, 406px" /></figure> <p>上为坎，为水，为凶险，下为艮，为山，水流上山，艰难之象。 故古人说，蹇者，难也，</p> <p>2）按六爻卦的象来看：&nbsp;&nbsp;&nbsp;子水在六爻，世上申金欲生顶上之水， 而五爻为路，有土来克，也是水想往上流而艰难之象。</p> <p>3）卦象含义：</p> <p>这个卦包含有艰难，危险，闭塞，不通，郁结，阻挠等意思。若是预测婚姻，男女都不吉。男测，财伏官下，妻被他人所夺，兄弟持世，世应之间又有兄弟阻隔，多有不顺， 女测，官来克世，也是不和之象，不过，喜五爻动而生世，五爻动，多是父母主事成婚， 男测，戌动合财生世， 女测，使鬼入墓而生世，五爻为家长，所以有此象。 预测事业，官运， 喜官鬼午火和戌土同动，则为多得领导提拔，官职可升，鬼独发，则凶。测灾有灾， 测官，他人得，而自己不得。六、地山谦：</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-67.png" alt="" class="wp-image-171" srcset="http://josejang.com/wp-content/uploads/2020/03/image-67.png 396w, http://josejang.com/wp-content/uploads/2020/03/image-67-300x151.png 300w" sizes="(max-width: 396px) 100vw, 396px" /></figure> <p>1）从卦本身的组合来看： </p> <p>外卦为坤，为大地，內卦为艮，为山，为高地，平地在上，高地在下，高让于低， 故名之为谦。 谦者，退也，退让之象。</p> <p>2）用六爻的象来看： &nbsp;&nbsp;&nbsp;&nbsp;世在五爻是尊位， 自己在高处，显要的地方， 而应在二爻低处， 子孙为克官之神，自己不想要官，欲退出官位，官临应爻，让位于别人。 不过这个卦里暗藏有玄机， 要根据其他变化组合而看 五爻临尊位，克应上官鬼， 谦让有时是假， 欲夺他人之位是真， 乃是以退为进。 3）卦象含义：</p> <p>这个卦包含有退让，以守为攻，谦虚，退缩，下降，返回等意思。</p> <p>七、雷山小过<strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-68.png" alt="" class="wp-image-172" srcset="http://josejang.com/wp-content/uploads/2020/03/image-68.png 449w, http://josejang.com/wp-content/uploads/2020/03/image-68-300x138.png 300w" sizes="(max-width: 449px) 100vw, 449px" /></figure> <p>1）从卦本身的组合来看： &nbsp;&nbsp;&nbsp;外卦为震，震为动，为怒，內卦为艮，为止， 发怒而有度，能适可而止，做事没有过头， 虽有小错而功大于过， 所以说小过者，过也。2）用六爻的象来看： &nbsp;&nbsp;&nbsp;&nbsp;官鬼持世， 以克五爻尊位，下逆上，为错误，好在世生应爻，又可以以德待人，先兵而后礼。此卦用来判断事业，对领导有不满情绪，对自己的部下则有宽容的一面，两兄弟夹世，容易被人攻击，被孤立起来。男测婚姻，多被人夺爱，对方有外心，女测婚姻，夫妻不和，自己出现情人。</p> <p>&nbsp;3）卦象含义：</p> <p>这个卦包含有求人，求助，帮忙，提问，修养，保护，自力更生，自我发展，白手起家等意思。这个卦包含有错误，失误，不足，欠缺，不完美等意思。 八、雷泽归妹</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-69.png" alt="" class="wp-image-173" srcset="http://josejang.com/wp-content/uploads/2020/03/image-69.png 428w, http://josejang.com/wp-content/uploads/2020/03/image-69-300x142.png 300w" sizes="(max-width: 428px) 100vw, 428px" /></figure> <p>1）从卦本身来看：</p> <p>外卦为震，为长男， 內卦为泽，为少女，长少不当而相配，少女在內，而外配长男， 出嫁之象， 因不配而少女居內，少女回家，所以谓之归妹。</p> <p>2）用六爻的象来看：&nbsp;&nbsp;&nbsp;&nbsp;世应比和都是土，为兄妹之象。 以十二地支排列， 丑先而大，戌后而小， 戌临阴爻为女，所以戌为丑之妹。 此卦官入墓于应，财又合应，财官皆归他人，所以为归妹。这个卦财在二爻为家中之财，自己之财， 合应，多为财被他人所得， 或者拿上自己的钱给予别人。不喜财动，也不喜应动。</p> <p>&nbsp;3）卦象含义：</p> <p>这个卦包含有归属，送给，外传，失去，流失，淘汰等意思。 &nbsp;</p> ",
            "protected":false
        },
        "excerpt":{
            "rendered":"<p>一、兑为泽 1、[卦象] 1）从卦本身的组合来看： 內外皆是兑，兑为泽，为口，两口相对，意思是在交谈，或者是在 [&hellip;]</p> ",
            "protected":false
        },
        "author":1,
        "featured_media":0,
        "comment_status":"open",
        "ping_status":"open",
        "sticky":false,
        "template":"",
        "format":"standard",
        "meta":{
            "_links_to":"",
            "_links_to_target":""
        },
        "categories":[
            14
        ],
        "tags":[

        ],
        "_links":{
            "self":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/posts/165"
                }
            ],
            "collection":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/posts"
                }
            ],
            "about":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/types/post"
                }
            ],
            "author":[
                {
                    "embeddable":true,
                    "href":"http://josejang.com/wp-json/wp/v2/users/1"
                }
            ],
            "replies":[
                {
                    "embeddable":true,
                    "href":"http://josejang.com/wp-json/wp/v2/comments?post=165"
                }
            ],
            "version-history":[
                {
                    "count":0,
                    "href":"http://josejang.com/wp-json/wp/v2/posts/165/revisions"
                }
            ],
            "wp:attachment":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/media?parent=165"
                }
            ],
            "wp:term":[
                {
                    "taxonomy":"category",
                    "embeddable":true,
                    "href":"http://josejang.com/wp-json/wp/v2/categories?post=165"
                },
                {
                    "taxonomy":"post_tag",
                    "embeddable":true,
                    "href":"http://josejang.com/wp-json/wp/v2/tags?post=165"
                }
            ],
            "curies":[
                {
                    "name":"wp",
                    "href":"https://api.w.org/{rel}",
                    "templated":true
                }
            ]
        }
    }
]
````

4.2、获取文章详情
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/posts/文章id
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数

参数名	说明	必填	类型	值可能性
context	上下文参数控制数据返回的格式	否	[string]	view:默认上下文，输出普通的用户内容。edit:更新文章时用来添加附加字段，包括 title_raw, content_raw, guid_raw 和自定义字段, 以适应文章编辑。parent:上下文在嵌入另一个（如文章作者）的返回时使用。这样做是为了使用用户数据的最小子集来减少响应大小。返回父字段作为一个 ID，而不是一个嵌入的文章，以确保我们不会遍历后面的整个层次结构。
成功示例：

````php
{
    "id":165,
    "date":"2020-03-10T16:06:43",
    "date_gmt":"2020-03-10T08:06:43",
    "guid":{
        "rendered":"http://josejang.com/?p=165"
    },
    "modified":"2020-03-10T16:07:10",
    "modified_gmt":"2020-03-10T08:07:10",
    "slug":"%e5%85%ad%e7%88%bb%e7%ad%91%e5%9f%ba%e7%ac%ac%e4%b8%80%e5%85%b3-%e5%85%ab%e5%8d%a6%e5%8d%a6%e8%b1%a1%e4%b9%8b%e5%85%91%e5%ae%ab%e5%85%ab%e5%8d%a6",
    "status":"publish",
    "type":"post",
    "link":"http://josejang.com/2020/03/10/%e5%85%ad%e7%88%bb%e7%ad%91%e5%9f%ba%e7%ac%ac%e4%b8%80%e5%85%b3-%e5%85%ab%e5%8d%a6%e5%8d%a6%e8%b1%a1%e4%b9%8b%e5%85%91%e5%ae%ab%e5%85%ab%e5%8d%a6/",
    "title":{
        "rendered":"六爻筑基第一关——八卦卦象之兑宫八卦"
    },
    "content":{
        "rendered":" <p><strong>一、</strong>兑为泽<strong></strong></p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-62.png" alt="" class="wp-image-166" srcset="http://josejang.com/wp-content/uploads/2020/03/image-62.png 299w, http://josejang.com/wp-content/uploads/2020/03/image-62-150x150.png 150w" sizes="(max-width: 299px) 100vw, 299px" /></figure> <p></p> <p>1）从卦本身的组合来看：</p> <p>內外皆是兑，兑为泽，为口，两口相对，意思是在交谈，或者是在争吵，与人交流，可以得益，但说多了又有言过其实之嫌。 2）用六爻的象来看：</p> <p>&nbsp;&nbsp;&nbsp;&nbsp;这个卦，世在六爻，主头部，脸面， 生五爻之金，金主声音，也是在谈论，讲话的意思。从六爻组合的角度来看，此卦，一般不宜女子预测婚姻，虽然官鬼生世，但世在六爻，官鬼在初爻，六爻为天，初爻为地，两人各居一方，分居两地之象。 若是求财， 财旺为佳，可以有大财得， 而且多是凭借三寸不烂之舌，得人钱财， 因为世为财库，财被我把持，兑为泽，为说的意思。若是男的预测婚姻， 财入二爻宅爻，妻虽然入了正位， 但兑为缺，家庭不能美满，会有许多遗憾，常常争吵不断，尤其是怕初爻，二爻同时发动，则妻子多有外遇。 3）卦象含义：</p> <p>这个卦包含有争吵，口舌，欠缺，不完美，破损，喜悦等意思。 二、泽水困　　</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-63.png" alt="" class="wp-image-167" srcset="http://josejang.com/wp-content/uploads/2020/03/image-63.png 306w, http://josejang.com/wp-content/uploads/2020/03/image-63-300x286.png 300w" sizes="(max-width: 306px) 100vw, 306px" /></figure> <p>1）从卦本身的组合来看：</p> <p>外卦为兑，为缺，內卦为坎，为水，泽中无水，水漏泽枯，处于困境。所以，名之为困。 2）用六爻的象来看：</p> <p>世在初爻，初爻为脚，主行走， 财持世，财主饮食，就是得到食物，在路上吃喝东西。但是，此卦六合，合则为留，为绊， 合世者为亥水，是被水所困。 古人说，困者，危也，没有水喝，自然为凶。 这个卦，财爻持世，又是六合卦，六爻预测一般为吉，但世在初爻，爻位比较低，很难发展到高层次。 这个卦，兄弟与二爻相合，多有朋友到家。 3）卦象含义：</p> <p>这个卦包含有被困，阻力，尽头，缓慢，迟滞，无法施展等意思。 三、泽地萃</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-64.png" alt="" class="wp-image-168" srcset="http://josejang.com/wp-content/uploads/2020/03/image-64.png 331w, http://josejang.com/wp-content/uploads/2020/03/image-64-300x261.png 300w" sizes="(max-width: 331px) 100vw, 331px" /></figure> <p>1）按卦本身的象来看：</p> <p>外卦为泽，为口，为水，內卦为坤，为地，合起来，就是地面上开口，有坑，水在地上聚成池塘，所以名之为萃。 古人说，萃者，聚也。 2）按六爻分爻来看：</p> <p>此卦为天地一统，天地合一之象。就是把宇宙大爆炸以后弥散的东西重新再聚到一起。 大家看看此卦的特点，六爻和初爻都是父母未土， 从六爻的爻位上来看，六爻为天，初爻为地，而父母本身也是天地的意思。 所以，六爻的父母未土就是天，初爻的父母未土就是地。大家看，六爻父母未土，从上往下看，是连续相生的， 六爻生五爻， 五爻生四爻， 四爻生三爻，三爻生二爻，二爻生初爻，父母未土又原来的天变成了地， 这就是天地相会， 回到了天地没有分开的宇宙状态。而这个过程必须要经过世爻才能完成。 世爻为一个卦的主体，天地会天下所有的东西都通过各种方式生到了我这里， 就等于自己拥有了天下的东西，众所归一，完全就是聚的意思。六十四卦象，这样的组合，只有这个卦。&nbsp;3）卦象含义：</p> <p>这个卦有汇聚，汇合，归属，集中，同意，和合，完美，收拾，归纳，团结等意思。 四、泽山咸</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-65.png" alt="" class="wp-image-169" srcset="http://josejang.com/wp-content/uploads/2020/03/image-65.png 426w, http://josejang.com/wp-content/uploads/2020/03/image-65-300x211.png 300w" sizes="(max-width: 426px) 100vw, 426px" /></figure> <p>1）从卦本身的组合来看：</p> <p>上卦为兑，为少女，下卦为艮，为少男， 阳下为刚，性向上， 兑上为阴，为柔，性向下， 阴阳正配，虽然在內外，但心通一处，为感应而通，所谓心有灵犀一点通， 古人说，咸者，感也，就是这个意思。</p> <p>2）用六爻的象来看：</p> <p>世在三爻，三爻为门，立足于通道，进出口。六爻为天，父母为信息，初爻为地，临父母也为信息， 天地信息都生我，就是与天地相通，可以感应到宇宙的所有信息。</p> <p>3）卦象含义：</p> <p>这个卦包含有感应，感觉，体会，理解，沟通，联系等意思。 若求财，男子预测，财旺则喜应动生世，因为应为财库，就是别人挣到钱，积攒起来拿到我这里来。 若是预测婚姻， 则不可， 男多找二婚，或是不是处女，或是女朋友到别人怀抱里去了。女的预测男朋友，不是有外遇，就是对方为有老婆的人。</p> <p>五、水山蹇</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-66.png" alt="" class="wp-image-170" srcset="http://josejang.com/wp-content/uploads/2020/03/image-66.png 406w, http://josejang.com/wp-content/uploads/2020/03/image-66-300x219.png 300w" sizes="(max-width: 406px) 100vw, 406px" /></figure> <p>上为坎，为水，为凶险，下为艮，为山，水流上山，艰难之象。 故古人说，蹇者，难也，</p> <p>2）按六爻卦的象来看：&nbsp;&nbsp;&nbsp;子水在六爻，世上申金欲生顶上之水， 而五爻为路，有土来克，也是水想往上流而艰难之象。</p> <p>3）卦象含义：</p> <p>这个卦包含有艰难，危险，闭塞，不通，郁结，阻挠等意思。若是预测婚姻，男女都不吉。男测，财伏官下，妻被他人所夺，兄弟持世，世应之间又有兄弟阻隔，多有不顺， 女测，官来克世，也是不和之象，不过，喜五爻动而生世，五爻动，多是父母主事成婚， 男测，戌动合财生世， 女测，使鬼入墓而生世，五爻为家长，所以有此象。 预测事业，官运， 喜官鬼午火和戌土同动，则为多得领导提拔，官职可升，鬼独发，则凶。测灾有灾， 测官，他人得，而自己不得。六、地山谦：</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-67.png" alt="" class="wp-image-171" srcset="http://josejang.com/wp-content/uploads/2020/03/image-67.png 396w, http://josejang.com/wp-content/uploads/2020/03/image-67-300x151.png 300w" sizes="(max-width: 396px) 100vw, 396px" /></figure> <p>1）从卦本身的组合来看： </p> <p>外卦为坤，为大地，內卦为艮，为山，为高地，平地在上，高地在下，高让于低， 故名之为谦。 谦者，退也，退让之象。</p> <p>2）用六爻的象来看： &nbsp;&nbsp;&nbsp;&nbsp;世在五爻是尊位， 自己在高处，显要的地方， 而应在二爻低处， 子孙为克官之神，自己不想要官，欲退出官位，官临应爻，让位于别人。 不过这个卦里暗藏有玄机， 要根据其他变化组合而看 五爻临尊位，克应上官鬼， 谦让有时是假， 欲夺他人之位是真， 乃是以退为进。 3）卦象含义：</p> <p>这个卦包含有退让，以守为攻，谦虚，退缩，下降，返回等意思。</p> <p>七、雷山小过<strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-68.png" alt="" class="wp-image-172" srcset="http://josejang.com/wp-content/uploads/2020/03/image-68.png 449w, http://josejang.com/wp-content/uploads/2020/03/image-68-300x138.png 300w" sizes="(max-width: 449px) 100vw, 449px" /></figure> <p>1）从卦本身的组合来看： &nbsp;&nbsp;&nbsp;外卦为震，震为动，为怒，內卦为艮，为止， 发怒而有度，能适可而止，做事没有过头， 虽有小错而功大于过， 所以说小过者，过也。2）用六爻的象来看： &nbsp;&nbsp;&nbsp;&nbsp;官鬼持世， 以克五爻尊位，下逆上，为错误，好在世生应爻，又可以以德待人，先兵而后礼。此卦用来判断事业，对领导有不满情绪，对自己的部下则有宽容的一面，两兄弟夹世，容易被人攻击，被孤立起来。男测婚姻，多被人夺爱，对方有外心，女测婚姻，夫妻不和，自己出现情人。</p> <p>&nbsp;3）卦象含义：</p> <p>这个卦包含有求人，求助，帮忙，提问，修养，保护，自力更生，自我发展，白手起家等意思。这个卦包含有错误，失误，不足，欠缺，不完美等意思。 八、雷泽归妹</p> <p><strong>1、[卦象]</strong></p> <figure class="wp-block-image size-large"><img src="http://josejang.com/wp-content/uploads/2020/03/image-69.png" alt="" class="wp-image-173" srcset="http://josejang.com/wp-content/uploads/2020/03/image-69.png 428w, http://josejang.com/wp-content/uploads/2020/03/image-69-300x142.png 300w" sizes="(max-width: 428px) 100vw, 428px" /></figure> <p>1）从卦本身来看：</p> <p>外卦为震，为长男， 內卦为泽，为少女，长少不当而相配，少女在內，而外配长男， 出嫁之象， 因不配而少女居內，少女回家，所以谓之归妹。</p> <p>2）用六爻的象来看：&nbsp;&nbsp;&nbsp;&nbsp;世应比和都是土，为兄妹之象。 以十二地支排列， 丑先而大，戌后而小， 戌临阴爻为女，所以戌为丑之妹。 此卦官入墓于应，财又合应，财官皆归他人，所以为归妹。这个卦财在二爻为家中之财，自己之财， 合应，多为财被他人所得， 或者拿上自己的钱给予别人。不喜财动，也不喜应动。</p> <p>&nbsp;3）卦象含义：</p> <p>这个卦包含有归属，送给，外传，失去，流失，淘汰等意思。 &nbsp;</p> ",
        "protected":false
    },
    "excerpt":{
        "rendered":"<p>一、兑为泽 1、[卦象] 1）从卦本身的组合来看： 內外皆是兑，兑为泽，为口，两口相对，意思是在交谈，或者是在 [&hellip;]</p> ",
        "protected":false
    },
    "author":1,
    "featured_media":0,
    "comment_status":"open",
    "ping_status":"open",
    "sticky":false,
    "template":"",
    "format":"standard",
    "meta":{
        "_links_to":"",
        "_links_to_target":""
    },
    "categories":[
        14
    ],
    "tags":[

    ],
    "_links":{
        "self":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/posts/165"
            }
        ],
        "collection":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/posts"
            }
        ],
        "about":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/types/post"
            }
        ],
        "author":[
            {
                "embeddable":true,
                "href":"http://josejang.com/wp-json/wp/v2/users/1"
            }
        ],
        "replies":[
            {
                "embeddable":true,
                "href":"http://josejang.com/wp-json/wp/v2/comments?post=165"
            }
        ],
        "version-history":[
            {
                "count":0,
                "href":"http://josejang.com/wp-json/wp/v2/posts/165/revisions"
            }
        ],
        "wp:attachment":[
            {
                "href":"http://josejang.com/wp-json/wp/v2/media?parent=165"
            }
        ],
        "wp:term":[
            {
                "taxonomy":"category",
                "embeddable":true,
                "href":"http://josejang.com/wp-json/wp/v2/categories?post=165"
            },
            {
                "taxonomy":"post_tag",
                "embeddable":true,
                "href":"http://josejang.com/wp-json/wp/v2/tags?post=165"
            }
        ],
        "curies":[
            {
                "name":"wp",
                "href":"https://api.w.org/{rel}",
                "templated":true
            }
        ]
    }
}
````

4.3、创建一篇文章
请求协议|请求方法：HTTP|POST
接口路径：/wp-json/wp/v2/posts
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数

参数名	说明	必填	类型	值可能性
title	文章标题	是	[string]	
content	文章内容	是	[string]	
excerpt_raw	文章摘要	否	[string]	
status	文章状态，默认为draft. (string) optional	否	[string]	draft:草稿,publish:发表,pending:待定, future:未来, private:私有
categories	文章标题分类，传分类id	否	[string]	
comment_status	文章的评论状态，默认为设置选项 ‘default_comment_status’的值, 或 ‘closed’. (string) optional	否	[string]	open:打开,closed:关闭
ping_status	文章的ping状态。用来确定用户是否可以提交pibgbacks和trackbacks，默认为设置选项 ‘default_ping_status’的值. (string) optional	否	[string]	open:打开,closed:关闭
sticky	文章的置顶状态。默认为 false. (boolean) optional	否	[string]	true:置顶,false:不置顶
author	文章的作者，作者可以为作者ID或作者对象，默认为当前作者。(object	string) optional	否	[string]
name	文章别名	否	[string]	
password	用来保护文章的密码，默认为空字符串 (string) optional	否	[string]	
type	文章类型，可以是: post, page, link, nav_menu_item, 或其他自定义文章类型. 默认为post. (string) optional	否	[string]	
post_parent	父级文章的ID，默认为0. (integer) optional	否	[string]	
post_format	文章格式，默认为standard. (string) optional	否	[string]	
menu_order	页面的顺序序号，默认为0 (integer) optional	否	[string]	
post_meta	文章的元数据，文章元数据应该是一个或多个元数据对象的数组，查看使用端点键值对创建文章元数据章节。 (array) optional	否	[string]	
date	发布文章的本地日期和时间. 日期应该是一个 RFC3339 时间戳(http://tools.ietf.org/html/rfc3339). 例如: 2014-01-01T12:20:52Z. 默认为本地日期和时间. (string) optional	否	[string]	
date_gmt	文章发布的UTC日期和时间，日期应该为一个 RFC3339 时间戳. 例如: 201401-01T12:20:52Z. 默认为当前的 GMT 日期和时间. (string) optional	否	[string]	
成功示例：

````php
{
    "id": 591,
    "date": "2020-04-14T10:33:02",
    "date_gmt": "2020-04-14T02:33:02",
    "guid": {
        "rendered": "http://josejang.com/?p=591",
        "raw": "http://josejang.com/?p=591"
    },
    "modified": "2020-04-14T10:33:02",
    "modified_gmt": "2020-04-14T02:33:02",
    "password": "",
    "slug": "",
    "status": "draft",
    "type": "post",
    "link": "http://josejang.com/?p=591",
    "title": {
        "raw": "1212",
        "rendered": "1212"
    },
    "content": {
        "raw": "1212",
        "rendered": "<p>1212</p>\n",
        "protected": false,
        "block_version": 0
    },
    "excerpt": {
        "raw": "",
        "rendered": "<p>1212</p>\n",
        "protected": false
    },
    "author": 22,
    "featured_media": 0,
    "comment_status": "open",
    "ping_status": "open",
    "sticky": false,
    "template": "",
    "format": "standard",
    "meta": {
        "_links_to": "",
        "_links_to_target": ""
    },
    "categories": [
        14
    ],
    "tags": [],
    "permalink_template": "http://josejang.com/2020/04/14/%postname%/",
    "generated_slug": "1212-2",
    "_links": {
        "self": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/posts/591"
            }
        ],
        "collection": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/posts"
            }
        ],
        "about": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/types/post"
            }
        ],
        "author": [
            {
                "embeddable": true,
                "href": "http://josejang.com/wp-json/wp/v2/users/22"
            }
        ],
        "replies": [
            {
                "embeddable": true,
                "href": "http://josejang.com/wp-json/wp/v2/comments?post=591"
            }
        ],
        "version-history": [
            {
                "count": 0,
                "href": "http://josejang.com/wp-json/wp/v2/posts/591/revisions"
            }
        ],
        "wp:attachment": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/media?parent=591"
            }
        ],
        "wp:term": [
            {
                "taxonomy": "category",
                "embeddable": true,
                "href": "http://josejang.com/wp-json/wp/v2/categories?post=591"
            },
            {
                "taxonomy": "post_tag",
                "embeddable": true,
                "href": "http://josejang.com/wp-json/wp/v2/tags?post=591"
            }
        ],
        "wp:action-publish": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/posts/591"
            }
        ],
        "wp:action-assign-categories": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/posts/591"
            }
        ],
        "wp:action-create-tags": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/posts/591"
            }
        ],
        "wp:action-assign-tags": [
            {
                "href": "http://josejang.com/wp-json/wp/v2/posts/591"
            }
        ],
        "curies": [
            {
                "name": "wp",
                "href": "https://api.w.org/{rel}",
                "templated": true
            }
        ]
    }
}
````

4.4、编辑文章
请求协议|请求方法：HTTP|PUT
接口路径：/wp-json/wp/v2/posts/文章id
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数

参数名	说明	必填	类型
备注	所有参数同创建文章	否	[string]
4.5、删除文章
请求协议|请求方法：HTTP|DELETE
接口路径：/wp-json/wp/v2/posts/文章id
接口使用状态：正常启用
请求头部：
| 头部标签 | 必填 | 头部内容 |
| :------------ | :------------ |
|Authorization|是|Bearer+token|

请求参数

参数名	说明	必填	类型	值可能性	限制	示例
force		是	[string]	false:指定文章被移动到回收站，默认,true:文章将被永久删除		
4.6、文章点赞 *
请求协议|请求方法：HTTP|POST
接口路径：/fapi/setpostlike.php
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数

参数名	说明	必填	类型
postid	文章id	是	[string]
4.7、获取文章版本
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/posts/文章id/revisions
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
成功示例：

````php
返回一个 200 OK 状态码和指定文章的版本
````

4.8、付费文章列表 *
请求协议|请求方法：HTTP|GET
接口路径：/fapi/getproductlist.php
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数

参数名	说明	必填	类型
numberposts	1	是	[string]
post_type	product	是	[string]
pagenum		是	[string]
5、文章评论
5.1、获取某文章所有的评论
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/comments
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数：

参数名	说明	必填	类型
post	文章ID	是	[string]
parent	父级评论id，0表示父级。添加该字段则获取文章的所有一级评论	否	[string]
成功示例：

````php
[
    {
        "id":8,
        "post":601,
        "parent":7,
        "author":1,
        "author_name":"admin",
        "author_url":"",
        "date":"2020-04-15T09:33:14",
        "date_gmt":"2020-04-15T01:33:14",
        "content":{
            "rendered":"<p>不错不错</p> "
        },
        "link":"http://josejang.com/2020/04/14/%e6%b7%bb%e5%8a%a0%e5%88%b0%e5%a1%94%e7%bd%97%e7%89%8c%e5%88%86%e7%b1%bb/#comment-8",
        "status":"approved",
        "type":"comment",
        "author_avatar_urls":{
            "24":"http://cn.gravatar.com/avatar/d87e1904bcb5bd85ad6882da2b2d9165?s=24&d=mm&r=g",
            "48":"http://cn.gravatar.com/avatar/d87e1904bcb5bd85ad6882da2b2d9165?s=24&d=mm&r=g",
            "96":"http://cn.gravatar.com/avatar/d87e1904bcb5bd85ad6882da2b2d9165?s=24&d=mm&r=g"
        },
        "meta":[

        ],
        "_links":{
            "self":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/comments/8"
                }
            ],
            "collection":[
                {
                    "href":"http://josejang.com/wp-json/wp/v2/comments"
                }
            ],
            "author":[
                {
                    "embeddable":true,
                    "href":"http://josejang.com/wp-json/wp/v2/users/1"
                }
            ],
            "up":[
                {
                    "embeddable":true,
                    "post_type":"post",
                    "href":"http://josejang.com/wp-json/wp/v2/posts/601"
                }
            ],
            "in-reply-to":[
                {
                    "embeddable":true,
                    "href":"http://josejang.com/wp-json/wp/v2/comments/7"
                }
            ]
        }
    },
]
````

5.2、获取指定评论详情
请求协议|请求方法：HTTP|POST
接口路径：/wp-json/wp/v2/comments/评论id
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
5.3、创建一条文章评论
注： 我记得测试这个接口的时候，一直提示权限有问题。后来因为其他事情没有继续往下处理了。哪位大神要是看到这里，帮测试一下，移动端是否可以创建评论。
请求协议|请求方法：HTTP|POST
接口路径：/wp-json/wp/v2/comments
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
请求参数
参数类型：表单

参数名	说明	必填	类型
post	文章id	是	[string]
content	评论内容	是	[string]
parent	评论所属父评论id，默认为0，即一级评论	是	[string]
author_name	评论者的名称	是	[string]
author_email	评论者邮箱	是	[string]
author_url	评论者url地址	是	[string]
6、媒体文件
6.1、获取所有的媒体信息
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/media
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
6.2、获取指定媒体ID的媒体信息
请求协议|请求方法：HTTP|GET
接口路径：/wp-json/wp/v2/media/媒体ID
请求头部：

头部标签	必填	头部内容
Authorization	是	Bearer+token
7、总结
通过接口的测试和了解，wp的主要功能包括：用户的登录注册、获取文章分类、获取文章详情、新增｜修改｜删除文章、评论文章、点赞文章和评论。
那么可以实现移动端资讯类app的基本应用。如果个人｜团队想构建一个比较简单的资讯类项目的话，应用wp框架是一个比较不错的选择。
说到iOS端创建文章的话，那就必须要用到富文本编辑器了。于是我就顺便了解了下iOS端富文本编辑器框架，发现这一类的却不多，个人感觉来说，比较好用的，bug比较少的就一个：WGEditor-mobile，谁有比较好的富文本编辑器框架的童鞋，欢迎留言！


