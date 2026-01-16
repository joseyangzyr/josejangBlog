---
title: "Python 百度链接提交：主动推送链接给百度，实现快速收录"
date: 2021-06-29
slug: baidu-link-push-python
description: "介绍如何使用 Python 调用百度资源平台接口，主动推送站点 URL 给百度搜索引擎，以提升新页面的收录速度。"
categories:
  - 工作
summary: "介绍如何使用 Python 调用百度资源平台接口，主动推送站点 URL 给百度搜索引擎，以提升新页面的收录速度。"
aliases:
  - /about-work/165.html
---

## 百度资源平台接口

百度资源接口提供了自动提交和手动提交两种方式

### 自动提交

自动提交提供了三种方式：

主动推送(实时) 推送接口：http://data.zz.baidu.com/urls?site=https://www.josejang.com&token=你的token

site 是推送的网站访问地址

token 是在搜索资源平台申请的推送用的准入密钥，要添写你自己的

自动推送 自动推送是百度搜索资源平台为提高站点新增网页发现速度推出的工具，安装自动推送JS代码的网页，在页面被访问时，页面URL将立即被推送给百度。这个在申请获取后添加到网站源码底部即可。

sitemap提交一般是新站刚建成后，有了部分内容，可通过网站生成sitemap文件一次提交给百度。

### 手动提交

手动提交分为普通数据提交和新站数据提交，和第三方工具类似，复制链接一个个添加，最后提交即可

## python推送

链接提交方式
1主动推送，用代码写推送链接，建议将当天产出的新链接推送给百度
2sitemap 以站点地图的方式推送，将sitemap 提交到百度，百度会周期性的抓取sitemap

推送实例Python版
多个URL实例

````python
import requests
urls = [
'https://www.josejang.com/about-work/159.html',
'https://www.josejang.com/about-work/157.html',
api = 'http://data.zz.baidu.com/urls?site=www.josejang.com&token=xxxxxxxxxxx';
response = resquests.post(api, data=
'\n'.join(urls))
print (res.content.docode())
````

其中site 后面跟站点域名，token 在百度站长后台管理，可以自己去获取
如果有多个URL，可以以文件的形式

````python
def push_url():
    url="/urls?site=www.josejang.com&token=xxxxxxxxxxxxx"
    file = {"file":open("./urls.txt","rb"}
    r = resquests.post("http://data.zz.baidu.com" + url,files=file)
    baiduresult = '推送结果%s \n" % (r.text).encode('utf-8')
    return baiduresult
if __name__=="__main__":
    push=push_url()
    print (push)
````

### python实现主动推送（实时）

````python
import requests
def baidu_push_urls(domain,urls):       #参数domain是你网站的域名，urls是要推送的网站链接地址，多条需要/n进行分割
    headers = {'Content-Type':'text/plain'}
    url = 'http://data.zz.baidu.com/urls?site={}&token={}'.format(domain,'你的token')
    return requests.post(url, headers = headers, data = urls, timeout = 3).text
````

好了我的主动推送百度链接接口好了，我们只要在我们网站发布文章的地方调用就可以了 调用返回结果如下:

````python
result = baidu_push_urls('www.josejang.com','https://www.josejang.com/about-work/165.html')
print(result)
````

打印结果：

````python
{"remain":2999,"success":1}
````

success=1表示调用成功，remain表示今天可以使用的调用接口次数

## python3 百度推送代码, 添加宝塔任务自动推送

````python
import requests
import re
import json

push_log_path = '/www/wwwroot/josejang.com/push_log.txt'		#日志文件，需要手动新建push_log.txt文件在网站根目录，并改成你的网站绝对路径

def get_sitemap_urls():
    ret = requests.get('https://www.josejang.com/sitemap.xml').text		#解析sitemap.xml中的链接地址
    re_loc = re.compile('<loc>(.*?)</loc>')
    urls = re.findall(re_loc,ret)
    return urls

def get_pushd_urls():
    with open(push_log_path) as f:
        content = f.read()
    urls = str(content).split('\n')
    return list(filter(lambda x : x, urls))


def get_no_push_urls(num=20):
    all_urls = get_sitemap_urls()
    pushed_urls = get_pushd_urls()
    urls = []
    for url in all_urls :
        if url in pushed_urls:
            continue
        if len(urls) == num :
            break
        urls.append(url)
    #返回urls
    return urls


def tianji_push(urls):
    '''
    天极推送
    '''
    headers = {'Content-Type':'text/plain'}
    api = 'http://data.zz.baidu.com/urls?site=https://www.josejang.com&token=*******'		#百度接口调用地址
    ret = requests.post(api,headers=headers, data = urls, timeout=3).text
    return ret

def write_to_log(url):
    '''
    写入文件
    '''
    with open(push_log_path,'a+') as f:
        f.write(url)

if __name__ == "__main__":
    urls = get_no_push_urls(5)		#每次推送链接的数量 根据每次采集文章数量进行调整
    for url in urls:
        try:
            ret = tianji_push(url)
            print(url,ret)
            data = json.loads(ret)
            success_realtime = data['remain']
            if success_realtime > 0 :
                write_to_log(url)
                print(url,'推送成功')
            else:
                print(url,'推送失败')
                break
        except:
            pass
    print('推送完成')
````

然后在宝塔后台添加计划任务
任务类型：shell脚本
任务名称：网站josejang.com百度推送
执行周期：执行周期 设置成你每次采集文章间隔时间
脚本内容：btpython /www/wwwroot/josejang.com/baidu.py



