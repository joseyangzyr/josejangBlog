---
title: "让ai有感情的读课文"
date: 2026-07-25
slug: gpt-sovits
description: "让ai有感情的读课文"
categories:
  - AI
summary: "让ai有感情的读课文"
aliases:
  - /ai/gpt-sovits.html
---

## 这是要用到的工具GPT-SoVITS
[git GPT-SoVITS](https://github.com/RVC-Boss/GPT-SoVITS)

[GPT-SoVITS_V4使用教程](https://zhuanlan.zhihu.com/p/1911781987422823810)

[免费在线体验](https://lj1995-gpt-sovits-proplus.hf.space/)


[整合包及模型下载链接](https://www.yuque.com/baicaigongchang1145haoyuangong/ib3g1e/dkxgpiy9zb96hob4#KTvnO)


## ai阅读思路

1. 用Qwen3.6 把文字拆分成段，并判断每一句话的情绪。
2. 然后用 GPT-SoVITS 根据ai判断的情绪切换对应的参考音频。
3. 最后全部缝合

## 代码

环境非常简单，本地把你的 GPT-SoVITS API 打开（默认是 http://127.0.0.1:9872）

下面代码只供参考，给你提供个思路，具体怎么才能跑需要根据你自己的环境进行配置。


```python
import os
import json
import requests
from openai import OpenAI
from pydub import AudioSegment
from tqdm import tqdm

# 1. 远程 Qwen API 配置
QWEN_API_KEY = "any_key"
QWEN_BASE_URL = "https://127.0.0.1:8080" 
QWEN_MODEL_NAME = "Qwen3.6-36B-A3B" 

# 2. 本地 GPT-SoVITS 地址
SOVITS_API_URL = "http://127.0.0.1:9872"

# 3. 情绪音频库映射（把你裁剪好的3-5秒各情绪wav路径和对应文字填在这里）
EMOTION_LIBRARY = {
    "平静": {"ref_audio": "C:/prompts/neutral.wav", "prompt_text": "今天的天气挺不错的。"},
    "喜悦": {"ref_audio": "C:/prompts/happy.wav", "prompt_text": "太好了！我们终于成功了！"},
    "悲伤": {"ref_audio": "C:/prompts/sad.wav", "prompt_text": "你怎么能这样丢下我，呜呜。"},
    "愤怒": {"ref_audio": "C:/prompts/angry.wav", "prompt_text": "住口！我不想再听你解释了！"}
}

client = OpenAI(api_key=QWEN_API_KEY, base_url=QWEN_BASE_URL)

def analyze_text(text_chunk):
    prompt = f"你是有声书导演。请将以下长文本按标点切成短句，并从{list(EMOTION_LIBRARY.keys())}中为每句话选一个最符合语境的情绪。严格返回JSON数组格式，不要包含Markdown标记。结构如：[{{'text': '...', 'emotion': '...'}}]\n文本：{text_chunk}"
    response = client.chat.completions.create(
        model=QWEN_MODEL_NAME,
        messages=[{"role": "user", "content": prompt}],
        temperature=0.3
    )
    return json.loads(response.choices.message.content.strip())

def generate_audio(text, emotion, idx):
    cfg = EMOTION_LIBRARY.get(emotion, EMOTION_LIBRARY["平静"])
    payload = {
        "text": text, "text_lang": "zh", "ref_audio_path": cfg["ref_audio"],
        "prompt_text": cfg["prompt_text"], "prompt_lang": "zh",
        "text_split_method": "cut5", "batch_size": 1
    }
    res = requests.post(f"{SOVITS_API_URL}/", json=payload)
    if res.status_code == 200:
        with open(f"temp_{idx}.wav", "wb") as f: f.write(res.content)
        return f"temp_{idx}.wav"
    return None


```

## 怎么让声音听起来更像真人？

既然我们要高频切换情绪，你的情绪 Prompt 音频库里的每个 wav 文件，长度最好严格控制在 3.5 秒到 5 秒之间。

每一段参考音频里的呼吸声一定要清晰，这样 GPT-SoVITS 才能把这种真人换气的节奏感完美复制到你的小说里。

