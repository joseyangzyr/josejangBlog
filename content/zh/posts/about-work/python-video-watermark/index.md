---
title: "视频批量加水印：Python 脚本实现方案"
date: 2022-03-13
slug: python-video-batch-watermark
description: "使用 Python 和 moviepy 实现视频批量添加水印的脚本方案，适合无管理员权限环境下的视频处理需求。"
categories:
  - 工作
tags:
  - Python
  - 视频处理
  - moviepy
summary: "使用 Python 和 moviepy 实现视频批量添加水印的脚本方案，适合无管理员权限环境下的视频处理需求。"
aliases:
  - /about-work/297.html
---

找了好几个批量视频加水印的软件，要么收费，要么需要administrator权限，公司电脑环境无法安装，试着查找了下python代码，果然找到，加个for循环调用videos目录下的所有视频文件，从而达到视频批量加水印的效果
不多说上代码：

````python
# -*- coding: utf-8 -*-

import moviepy.editor as mp
import os

def markvideo(filename, outname):
        video = mp.VideoFileClip(filename)
        logo = (mp.ImageClip("D:/video_logo.png")	#准备log图片
                .set_duration(video.duration) # 水印持续时间
                .resize(height=100) # 水印的高度，会等比缩放
                .margin(left=8, bottom=8, opacity=0) # 水印边距和透明度
                .set_pos(("left","bottom"))) # 水印的位置

        final = mp.CompositeVideoClip([video, logo])
        # mp4文件默认用libx264编码， 比特率单位bps
        final.write_videofile(outname+'.mp4', codec="libx264", bitrate="10000000")

if __name__ == '__main__':
        dirnum = 0
        filenum = 0
        path = 'D:/videos/'	#本地视频位置
        for lists in os.listdir(path):
                sub_path = os.path.join(path, lists)
                print(sub_path)
                if os.path.isfile(sub_path):
                        markvideo(str(sub_path), str(filenum))
                        filenum = filenum + 1
                elif os.path.isdir(sub_path):
                        dirnum = dirnum + 1

        print('该目录下有%s个文件夹' % dirnum )
        print('共 %s 个文件' % filenum)
````

