---
title: "如何去背？Python rembg 实现 AI 自动去背景的完整教程"
date: 2025-07-05
description: "基于 rembg（U-2-Net）的 Python 自动去背景完整示例，支持批量处理 PNG/JPG/WebP，适合本地或服务器使用。"
categories: ["AI"]
tags: ["Python", "rembg", "去背景", "图像处理", "AI"]
summary: "基于 rembg（U-2-Net）的 Python 自动去背景完整示例，支持批量处理 PNG/JPG/WebP，适合本地或服务器使用。"
aliases:
  - /ai/356.html
  - /ai/python-rembg-remove-background.html
---

rembg 是基于开源 AI 模型 U-2-Net 的图像去背景工具，支持命令行、Python 脚本、批量处理，轻量且精准。

````python
import os
from rembg import remove

input_folder = 'images'
output_folder = 'results'

os.makedirs(output_folder, exist_ok=True)

for filename in os.listdir(input_folder):
    if filename.lower().endswith(('.png', '.jpg', '.jpeg', '.webp')):
        with open(os.path.join(input_folder, filename), 'rb') as i:
            input_bytes = i.read()
        output_bytes = remove(input_bytes)
        output_path = os.path.join(output_folder, f"{os.path.splitext(filename)[0]}.png")
        with open(output_path, 'wb') as o:
            o.write(output_bytes)
        print(f"{filename} 去背景完成")
````

## rembg 支持的格式

输入：JPG、PNG、WEBP、BMP 等

输出：PNG（透明背景）



