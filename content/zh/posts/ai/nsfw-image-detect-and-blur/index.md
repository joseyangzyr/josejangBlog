---
title: "不良图片识别并自动打码"
description: "基于 YOLO 的不良图片识别方案，对检测到的敏感区域进行自动打码，适用于内容审核、隐私保护等场景。"
date: 2025-03-12
categories: ["AI"]
tags: ["nsfw", "image-processing", "yolo", "opencv", "automation"]
summary: "基于 YOLO 的不良图片识别方案，对检测到的敏感区域进行自动打码，适用于内容审核、隐私保护等场景。"
aliases:
  - /ai/321.html
  - /ai/nsfw-image-detect-and-blur.html
---

检测指定文件夹下是否存在不适宜内容，如果存在就个图片打个马赛克。

可以用于自动内容审核、网站敏感内容管理、隐私保护、自动化内容过滤、家长控制等场景。

````python
import os
import cv2
import numpy as np
from ultralytics import YOLO
from PIL import Image

# 配置参数
MODEL_PATH = "erax_nsfw_yolo11m.pt"  # 你的 NSFW 识别模型路径
IMAGE_EXTENSIONS = (".jpg", ".jpeg", ".png", ".bmp")  # 允许的图片格式
BLUR_KERNEL_SIZE = (45, 45)  # 模糊程度，可调节

# 初始化 YOLO 模型
model = YOLO(MODEL_PATH)

def detect_nsfw(image_path):
    """检测图片中的 NSFW 内容，并返回需要模糊的区域"""
    results = model(image_path)  # 进行 NSFW 识别

    image = cv2.imread(image_path)
    has_nsfw = False  # 记录是否检测到 NSFW

    for result in results:
        for box in result.boxes.xyxy:  # 遍历检测到的 NSFW 区域
            x1, y1, x2, y2 = map(int, box)
            has_nsfw = True  # 发现 NSFW 内容

            # 对该区域进行模糊处理
            region = image[y1:y2, x1:x2]
            blurred = cv2.GaussianBlur(region, BLUR_KERNEL_SIZE, 0)
            image[y1:y2, x1:x2] = blurred  # 替换原始图像区域

    return image, has_nsfw

def process_images_in_folder(root_folder):
    """ 递归查找所有图片，检测并模糊 NSFW """
    for foldername, _, filenames in os.walk(root_folder):  # 遍历文件夹
        for filename in filenames:
            if filename.lower().endswith(IMAGE_EXTENSIONS):  # 只处理图片
                image_path = os.path.join(foldername, filename)
                print(f"🔍 处理文件: {image_path}")

                blurred_image, has_nsfw = detect_nsfw(image_path)
                
                if has_nsfw:
                    print(f"🚨 发现 NSFW 内容，进行模糊处理...")
                    cv2.imwrite(image_path, blurred_image)  # **直接覆盖原文件**
                    print(f"✅ 处理完成，已覆盖: {image_path}")
                else:
                    print("✅ 未发现不正当内容，跳过")

# 运行检测
process_images_in_folder("images")  # 请替换成你的图片文件夹路径
````

这段代码的主要功能是在一个文件夹内递归查找图片，并使用 YOLO 识别 NSFW（不适宜内容）区域，对这些区域进行模糊处理。

📌 代码功能分析
加载 YOLO 模型，用于识别 NSFW 内容。
遍历文件夹，查找所有 jpg/png/bmp 等格式的图片。
对每张图片进行 NSFW 检测，如果发现不适宜内容，则模糊处理。
处理后的图片直接覆盖原文件。

erax_nsfw_yolo11m.pt 是一个训练好的 NSFW 识别模型，用 YOLO 框架加载。
你需要确保这个模型文件在代码同级目录下，否则会报错。
这里可以下载这个模型，有能力的也可以自行训练：

````
https://huggingface.co/erax-ai/EraX-NSFW-V1.0
````

所需Python 依赖：

````
pip install ultralytics opencv-python numpy pillow
````

