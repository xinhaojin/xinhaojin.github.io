---
title: 百度短语音转文字API调用
tags: []
categories:
  - python
date: 2023-03-23 13:30:00
---
百度的语音转文字API只支持最长60s的语音，如果要识别长语音，需要先切分，识别后再把结果合并。音频采样率和声道数好像也有影响，需要做修改，以下是一个成功案例。
## 源代码
```python
from aip import AipSpeech  # baidu_aip
from pydub import AudioSegment
import os
#输入音频，输出文本
def audio_to_text(audio_path):
    APP_ID = ''
    API_KEY = ''
    SECRET_KEY = ''
    client = AipSpeech(APP_ID, API_KEY, SECRET_KEY)
    audio = AudioSegment.from_wav(audio_path)
    #获取时长
    audio_length = audio.duration_seconds
    #采样率改成16000
    audio=audio.set_frame_rate(16000)
    #声道改成单声道
    audio=audio.set_channels(1)
    max_len=60
    audios=[]
    if audio_length>max_len:
    #分片，每片60s
        for i in range(int(audio_length//max_len)):
            audios.append(audio[i*max_len*1000:(i+1)*max_len*1000])
        audios.append(audio[int(audio_length//max_len)*max_len*1000:])
    else:
        audios.append(audio)
    #调用api
    results=''
    for i in range(len(audios)):
        audios[i].export('temp.wav',format='wav')
        with open('temp.wav', 'rb') as fp:
            result = client.asr(fp.read(), 'wav', 16000, {'dev_pid': 1537,})
        if result['err_no'] == 0:
            print(result['result'][0])
            results=results+result['result'][0]
        else:
            print(result['err_msg'])    
    os.remove('temp.wav')
    return results
```