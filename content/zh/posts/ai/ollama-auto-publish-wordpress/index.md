---
title: "根据关键词让 Ollama 帮我生成文章和图片并发布到 WordPress"
date: 2025-03-11
description: "使用 Ollama + Stable Diffusion 实现从关键词到文章、图片，再自动发布到 WordPress 的完整自动化流程。"
categories: ["AI"]
tags: ["ollama", "wordpress", "automation", "ai-writing"]
summary: "使用 Ollama + Stable Diffusion 实现从关键词到文章、图片，再自动发布到 WordPress 的完整自动化流程。"
aliases:
  - /ai/318.html
  - /ai/ollama-auto-publish-wordpress.html
---

## 📌 代码整体流程

本代码的主要功能是 自动生成文章并发布到 WordPress，完整流程如下：

1. Ollama 生成文章（含 标题、HTML 正文、Lora Prompt）
2. 翻译 Prompt（确保 AI 生成图片时使用英文描述）
3. Stable Diffusion 生成 AI 图片
4. 上传图片到 WordPress
5. 发布文章到 WordPress

````python
import requests
import json
import torch
from diffusers import LCMScheduler, AutoPipelineForText2Image
from PIL import Image
import os
from mimetypes import guess_type

def check_ollama_service():
    """检查 Ollama 服务是否可用"""
    url = "http://localhost:11434"
    try:
        response = requests.get(url, timeout=3)
        return response.status_code == 200
    except requests.RequestException:
        return False

def generate_article(keyword):
    """使用 Ollama 生成文章（HTML 正文 + Lora Prompt）"""
    url = "http://localhost:11434/api/chat"
    model = "qwen2.5"
    system_prompt = "你是一个专业的文章创作者，请输出完整的文章，包括标题、HTML 格式的正文和 Lora 生成图片的 Prompt。"
    user_prompt = f"""
    请根据以下关键词撰写一篇完整的文章：
    关键词：{keyword}
    返回 JSON 格式：
    {{
        "title": "文章标题",
        "wp_content": "<p>文章内容（HTML 格式）</p>",
        "Prompt": "Lora 生成图片的 Prompt"
    }}
    """
    try:
        response = requests.post(url, json={
            "model": model,
            "messages": [
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": user_prompt}
            ],
            "stream": False
        }, headers={"Content-Type": "application/json"})
        if response.status_code == 200:
            content = response.json().get("message", {}).get("content", "{}" ).strip()
            return json.loads(content)
    except (requests.RequestException, json.JSONDecodeError) as e:
        print(f"❌ 文章生成失败: {e}")
    return {"title": "未知文章", "wp_content": "<p>无法解析文章内容</p>", "Prompt": ""}

def translate_with_ollama(text):
    """使用 Ollama 翻译文本"""
    url = "http://localhost:11434/api/chat"
    try:
        response = requests.post(url, json={
            "model": "qwen2.5",
            "messages": [
                {"role": "system", "content": "你是一个翻译助手，专门将中文翻译成流畅的英文。"},
                {"role": "user", "content": f"请将以下内容翻译成英文：{text}"}
            ],
            "stream": False
        }, headers={"Content-Type": "application/json"})
        if response.status_code == 200:
            return response.json().get("message", {}).get("content", text)
    except requests.RequestException as e:
        print(f"❌ 翻译失败: {e}")
    return text

def generate_image(prompt, output_path="generated_image.png"):
    """使用 Diffusers 生成 AI 图片"""
    model_id = "stabilityai/stable-diffusion-2-1"
    pipeline = AutoPipelineForText2Image.from_pretrained(
        model_id, torch_dtype=torch.float16
    ).to("cuda" if torch.cuda.is_available() else "cpu")
    pipeline.scheduler = LCMScheduler.from_config(pipeline.scheduler.config)
    image = pipeline(prompt=prompt, num_inference_steps=30).images[0]
    image.save(output_path)
    return output_path

def upload_image_to_wordpress(image_path, wordpress_url, username, password):
    """上传图片到 WordPress"""
    media_endpoint = f"{wordpress_url}/wp-json/wp/v2/media"
    file_name = os.path.basename(image_path)
    mime_type = guess_type(image_path)[0] or "application/octet-stream"
    with open(image_path, "rb") as img_file:
        files = {"file": (file_name, img_file, mime_type)}
        headers = {"Content-Disposition": f'attachment; filename="{file_name}"'}
        response = requests.post(media_endpoint, auth=(username, password), headers=headers, files=files)
    if response.status_code == 201:
        return response.json().get("source_url")
    print(f"❌ 图片上传失败: {response.status_code} - {response.text}")
    return None

def post_to_wordpress(title, content, image_url, wordpress_url, username, password):
    """发布文章到 WordPress"""
    wp_endpoint = f"{wordpress_url}/wp-json/wp/v2/posts"
    headers = {"Content-Type": "application/json"}
    auth = (username, password)
    if image_url:
        content = f'<img src="{image_url}" alt="{title}" /><br>' + content
    post_data = {"title": title, "content": content, "status": "publish"}
    response = requests.post(wp_endpoint, auth=auth, headers=headers, json=post_data)
    return response.json()

def main():
    if not check_ollama_service():
        print("❌ Ollama 服务未启动，请检查 Ollama 是否在本地运行！")
        return
    keyword = "人工智能的发展趋势"
    wordpress_url = "https://www.josejang.com"
    username = "josejang"
    password = "应用密码！"

    article_data = generate_article(keyword)
    title = article_data["title"]
    content = article_data["wp_content"]
    image_prompt = article_data["Prompt"]
    
    translated_prompt = translate_with_ollama(image_prompt)
    image_path = generate_image(translated_prompt)
    
    image_url = upload_image_to_wordpress(image_path, wordpress_url, username, password)
    if not image_url:
        print("⚠️ 图片上传失败，文章将不包含图片")
    
    post_response = post_to_wordpress(title, content, image_url, wordpress_url, username, password)
    print("✅ 文章发布结果:", post_response)

if __name__ == "__main__":
    main()
````

✅ 文章生成 → ✅ Prompt 翻译 → ✅ AI 生成图片 → ✅ 上传图片 → ✅ 文章发布

大概就这个思路，想要批量发布文章的自己弄的循环或定时，有问题可以留言或找我交流

