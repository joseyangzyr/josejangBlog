---
title: "一个非常简单的 Selenium 提交历史记录脚本"
date: 2020-01-12
slug: selenium-submit-history
description: "使用 Selenium 自动向 Wayback Machine 提交页面快照的简单脚本，适合做站点历史留档或定期备份。"
categories:
  - 工作
summary: "使用 Selenium 自动向 Wayback Machine 提交页面快照的简单脚本，适合做站点历史留档或定期备份。"
aliases:
  - /about-work/73.html
  - /about-work/selenium-submit-history.html
---

我的运行环境 Python2.1.17
我使用的是firefox浏览器
没有太多时间，就没写登录仅仅是个简单的提交，浏览器先登录archive.org，保存账号密码，以便下次打开网站自动登录。然后在crontab中设置定时运行。
![archive页面提交](1.png)

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support.expected_conditions import presence_of_element_located
import time
with webdriver.Firefox() as driver:
    wait = WebDriverWait(driver, 10)
    driver.get("https://web.archive.org/save/")
    driver.find_element_by_xpath("/html/body[@class='navia']/div[@class='container']/div[@class='row'][2]/div[@class='col-md-6 col-md-offset-3 text-center']/div[@id='web_save_div']/form[@id='web-save-form']/div[1]/input[@id='capture_outlinks']").click()
    driver.find_element_by_xpath("/html/body[@class='navia']/div[@class='container']/div[@class='row'][2]/div[@class='col-md-6 col-md-offset-3 text-center']/div[@id='web_save_div']/form[@id='web-save-form']/div[3]/input[@id='capture_screenshot']").send_keys(Keys.SPACE)
    inputurl = driver.find_element_by_xpath("/html/body[@class='navia']/div[@class='container']/div[@class='row'][2]/div[@class='col-md-6 col-md-offset-3 text-center']/div[@id='web_save_div']/form[@id='web-save-form']/input[@id='web-save-url-input']")
    inputurl.send_keys("http://www.josejang.com")
    Save_Page = driver.find_element_by_xpath("/html/body[@class='navia']/div[@class='container']/div[@class='row'][2]/div[@class='col-md-6 col-md-offset-3 text-center']/div[@id='web_save_div']/form[@id='web-save-form']/input[@class='web-save-button web_button web_text']")
    Save_Page.click()
    time.sleep(60)
```

