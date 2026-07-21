---
title: "Gemma4 越狱版 使用指南：安装、模型下载与注意事项"
date: 2026-07-20T15:30:00+08:00
slug: gemma4-jailbreak-guide
description: "介绍 Gemma 越狱版模型的特点、安装方式、运行方法以及与官方 Gemma 模型的区别。"
categories:
  - AI
summary: "介绍 Gemma 越狱版模型的特点、安装方式、运行方法以及与官方 Gemma 模型的区别。"
aliases:
  - /ai/gemma4-jailbreak-guide.html
---


https://huggingface.co/douyamv/Gemma-4-31B-JANG_4M-CRACK-GGUF

![](huggingface-url.png)

国内源：
https://hf-mirror.com/douyamv/Gemma-4-31B-JANG_4M-CRACK-GGUF



## 本地部署
由于我本地环境是windows2022，这里只测试windows用户安装

### Windows用户
管理员PowerShell：
```
irm https://unsloth.ai/install.ps1 | iex
```

[安装教程](https://unsloth.ai/docs/zh/kai-shi-shi-yong/install)

安装完成后，启动Unsloth Studio，浏览器打开 http://127.0.0.1:8888 就能看到界面。
![启动](powershell.png)


把下载好的模型放入模型文件夹
C:\Users\Administrator\\.cache\huggingface\hub\

![Gub页面截图](unsloth_hub_on_device.png)


然后选择你要刚刚下载的模型
![选择模型](select_model.png)

如果你有条件链接huggingface.co 可以hug里搜索 JANG_4M-CRACK 进行下载


加载后就可以使用啦, 快去试试吧！

## 测试
看看是否突破限制？
![Gemma4越狱版模型测试](dome1.png)

这是我使用ollama 配置 Gemma4 模型的的效果：
![ollama使用Gemma4测试](dome_ollama_gemma4.png)

这是我vLLM中 Qwen3.6-36B-A3B 模型的效果：
![Qwen3.6-36B-A3B测试](dome_vllm_qwen3.6.png)