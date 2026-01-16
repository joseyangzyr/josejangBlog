---
title: "局域网内配置 Ollama 远程服务"
date: 2025-02-28
description: "在局域网内配置 Ollama 远程访问，使其他设备可调用本地模型服务"
tags: ["Ollama", "AI", "本地部署"]
draft: false
aliases:
  - /ai/315.html
  - /ai/ollama-lan-remote.html
---

---

默认情况下，Ollama 服务仅在本地运行，不对外提供服务。要使 Ollama 服务能够对外提供服务，你需要设置以下两个环境变量：

````
OLLAMA_HOST=0.0.0.0
OLLAMA_ORIGINS=*
````

1、在 Windows 上配置
在 Windows 上，Ollama 会继承你的用户和系统环境变量。

①通过任务栏退出正在运行的 Ollama 应用。
②打开 设置（Windows 11） 或 控制面板（Windows 10），搜索“环境变量”。
③编辑你的用户环境变量：

新建或编辑 OLLAMA_HOST，值设为 0.0.0.0
新建或编辑 OLLAMA_ORIGINS，值设为 *

④保存设置并重新启动 Ollama 应用，使配置生效。
⑤如果你习惯命令行，也可以使用命令行方式（可选）

````
setx OLLAMA_HOST "0.0.0.0"
setx OLLAMA_ORIGINS "*"
````

2、在 MacOS 上配置
①打开命令行终端，输入以下命令：

````
launchctl setenv OLLAMA_HOST "0.0.0.0"
launchctl setenv OLLAMA_ORIGINS "*"
````

②重启 Ollama 应用，使配置生效。

3、在 Linux 上配置
在 Linux 上，如果 Ollama 作为 systemd 服务运行，需要通过 systemctl 设置环境变量：
①编辑 systemd 服务配置

````
systemctl edit ollama.service
````

②在 [Service] 部分添加以下内容，保存并退出

````
[Service]
Environment="OLLAMA_HOST=0.0.0.0"
Environment="OLLAMA_ORIGINS=*"
````

③重新加载 systemd 并重启 Ollama：

````
systemctl daemon-reload
systemctl restart ollama
````

Ollama 服务 IP 地址
配置完成后，Ollama 服务将能在当前网络（如家庭 Wifi）中提供服务，你可以在局域网内的其他设备上连接到 Ollama。

通常，Ollama 服务的 IP 地址类似于 192.168.X.X，你可以在你的客户端或者插件中设置 API Host 为：

http://192.168.X.X:11434
![](Snipaste_2025-02-28_18-24-44.png)
连接远程 Ollama
我们可以使用 Chatbox 软件或 Page Assist 浏览器插件来连接远程 Ollama。

【Chatbox】【Page Assist】
Ollama 支持多个 UI 界面（查看官方支持列表），这里我们使用 Chatbox 来连接远程 Ollama。

安装 Chatbox 后打开“设置”，“模型提供方”选择：ollama api，“API域名”填写远程服务的IP地址和端口，例如我的远程电脑的IP是 192.168.31.30，这里的值就是：http://192.168.31.30:11434 如果局域网没有配置错误，就能看到你远程运行的模型，选择其中一个。

我这里是为了给siri配一个AI助手，快捷指令分享：

https://www.icloud.com/shortcuts/804d55cf5d0b4a90aaaffeabfb7b3963

![](IMG_5145.png)
将ip地址改成你自己的，获取内容中的请求体 model改成你的模型，
查看模型列表：

````
ollama list
````

关闭远程访问
如果不想让 Ollama 继续对外提供服务，可以使用这些命令取消远程访问。

【Windows】【MacOS】

````
setx OLLAMA_HOST "" 
setx OLLAMA_ORIGINS ""
````

注意事项
1、确保你的设备在同一 WiFi 网络下
2、可能需要在防火墙中允许 Ollama 服务的端口（默认为 11434），具体取决于你的操作系统和网络环境
3、为避免安全风险，请不要将 Ollama 服务暴露在公共网络中，家庭 WiFi 网络是一个相对安全的环境
4、如果服务运行不稳定，可以尝试 sudo systemctl restart ollama（Linux）或重启应用（Mac/Windows）

