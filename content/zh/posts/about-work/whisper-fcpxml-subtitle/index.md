---
title: "将音频生成 srt 和 fcpxml 字幕文件"
date: 2025-03-12T16:20:00+08:00
slug: "audio-to-srt-fcpxml"
description: "使用 Whisper 对音频进行语音识别，自动生成 SRT 字幕文件和 FCPXML 字幕文件，适用于 Final Cut Pro 的字幕制作流程。"
summary: "使用 Whisper 对音频进行语音识别，自动生成 SRT 字幕文件和 FCPXML 字幕文件，适用于 Final Cut Pro 的字幕制作流程。"
categories:
  - 工作
  - AI
tags:
  - Whisper
  - 字幕生成
  - SRT
  - FCPXML
  - Python
aliases:
  - /about-work/320.html
---

**Whisper + FCPXML 自动字幕生成器解析**
对音频文件进行语音识别并生成字幕文件 (SRT)，(fcpxml)
对应剪映用户可略过GG
还是先上代码：

````python
import whisper
import srt
import webrtcvad
import wave
import numpy as np
from datetime import timedelta
from pydub import AudioSegment
import os

# 加载 Whisper 模型
model = whisper.load_model("base")

def convert_mp3_to_wav(mp3_path):
    """ 将 MP3 转换为 WAV (16kHz, 单声道, 16-bit PCM) """
    wav_path = mp3_path.replace(".MP3", ".wav").replace(".mp3", ".wav")
    audio = AudioSegment.from_file(mp3_path, format="mp3")
    audio = audio.set_channels(1).set_frame_rate(16000).set_sample_width(2)  # 16-bit PCM
    audio.export(wav_path, format="wav")
    return wav_path

def is_voice(frame, vad):
    """ 判断音频帧是否包含人声 """
    return vad.is_speech(frame, sample_rate=16000)

def vad_filter(audio_path):
    """ 处理音频并检测有人声的片段 """
    vad = webrtcvad.Vad(2)  # 0 = 最宽松, 3 = 最严格

    with wave.open(audio_path, "rb") as wf:
        rate = wf.getframerate()
        assert rate == 16000, f"音频采样率错误: {rate}Hz (必须是 16000Hz)"
        
        width = wf.getsampwidth()
        assert width == 2, f"音频格式错误: 需要 16-bit PCM, 但读取到 {width * 8}-bit"
        
        channels = wf.getnchannels()
        assert channels == 1, f"音频通道错误: 需要单声道, 但读取到 {channels} 声道"

        frames = wf.readframes(wf.getnframes())

    # 转换音频数据
    audio_array = np.frombuffer(frames, dtype=np.int16)

    # 设置帧长（必须是 10ms, 20ms, 30ms）
    frame_duration = 30  # 30ms
    frame_size = int(rate * frame_duration / 1000)  # 30ms 的采样点数量
    frame_bytes = frame_size * 2  # 每个采样点 2 字节 (16-bit PCM)

    non_silent_ranges = []

    # 遍历音频帧
    for i in range(0, len(audio_array) * 2, frame_bytes):  # 以字节为单位切割
        frame = frames[i:i + frame_bytes]

        # 忽略过短的帧
        if len(frame) < frame_bytes:
            break

        if is_voice(frame, vad):
            start_time = (i / 2) / rate  # 计算开始时间
            end_time = ((i + frame_bytes) / 2) / rate  # 计算结束时间
            non_silent_ranges.append((start_time, end_time))

    return non_silent_ranges

def generate_fcpxml(subtitles, fcpxml_path, media_path):
    """ 生成 FCPXML 文件 """
    fcpxml_template = f'''<?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE fcpxml>
    <fcpxml version="1.9">
        <resources>
            <format id="r1" frameDuration="1/24s" />
            <asset id="r2" name="{os.path.basename(media_path)}" src="file://{media_path}" />
        </resources>
        <library>
            <event name="自动字幕">
                <project name="{os.path.basename(media_path)}">
                    <sequence format="r1" duration="{subtitles[-1].end.total_seconds()}s">
                        <spine>
'''  
    
    for sub in subtitles:
        start = sub.start.total_seconds()
        duration = (sub.end - sub.start).total_seconds()
        
        fcpxml_template += f'''
                            <caption start="{start}s" duration="{duration}s" format="itt" lane="1" offset="{start}s" role="subtitle">{sub.content}</caption>
                        '''
    
    fcpxml_template += '''
                        </spine>
                    </sequence>
                </project>
            </event>
        </library>
    </fcpxml>
    '''
    
    with open(fcpxml_path, "w", encoding="utf-8") as f:
        f.write(fcpxml_template)
    print(f"FCPXML 生成完成: {fcpxml_path}")

# 你的 transcribe_audio 函数中，调用 generate_fcpxml

def transcribe_audio(file_path):
    """ 处理音频，转换字幕并过滤无声部分 """
    # 转换 MP3 到 WAV
    if file_path.lower().endswith(".mp3"):
        file_path = convert_mp3_to_wav(file_path)

    non_silent_ranges = vad_filter(file_path)
    
    # 运行 Whisper 识别
    result = model.transcribe(file_path, language="zh", task="transcribe")

    subtitles = []
    for segment in result["segments"]:
        start, end, text = segment["start"], segment["end"], segment["text"]
        
        # 只保留有人声的字幕
        if any(s <= start <= e for s, e in non_silent_ranges):
            subtitles.append(srt.Subtitle(index=len(subtitles) + 1,
                                          start=timedelta(seconds=start),
                                          end=timedelta(seconds=end),
                                          content=text))

    # 生成 SRT 文件
    srt_path = file_path.replace(".wav", ".srt")
    with open(srt_path, "w", encoding="utf-8") as f:
        f.write(srt.compose(subtitles))
    print(f"字幕 SRT 生成完成: {srt_path}")

    # 生成 FCPXML 文件
    fcpxml_path = file_path.replace(".wav", ".fcpxml")
    generate_fcpxml(subtitles, fcpxml_path, file_path)

# 运行
transcribe_audio("audio.MP3")  # 替换为你的音频文件名
````

代码的主要功能是：

使用 OpenAI Whisper 识别音频（支持 MP3、WAV）。
通过 WebRTC VAD 过滤无声片段，确保只提取有人声的部分。
生成 SRT 字幕文件（兼容大部分视频编辑软件）。
生成 Final Cut Pro X (FCPX) 可用的 FCPXML 字幕文件，方便直接导入到视频编辑软件中。

采用 60fps (frameDuration="1/60s") 适配高帧率视频。可自行修改为24fps，或你想要的帧率

如果音频是 MP3，先转换为 16kHz, 单声道, 16-bit PCM 格式的 WAV。

修改最后一行的

````
transcribe_audio("audio.MP3")
````

输入音频文件路径，自动执行 MP3 转换、语音识别、字幕生成。

这段代码可以用于 自动字幕生成，特别适合 视频博主、剪辑师 直接导入 Final Cut Pro X，省去手动调整字幕的麻烦。🚀

如果你有更多优化需求，比如 字体样式、字幕位置，可以进一步修改 generate_fcpxml() 逻辑。💡

