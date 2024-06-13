---
title: LDA模型(gensim)中文主题提取
index_img: https://xinhaojin.github.io/imgs-host/past/2021/05/image-28-1024x646.png
tags: []
id: '1708'
categories:
  - - python
date: 2021-05-20 15:29:46
---

#### 安装依赖

python版本3.7

    pip install gensim
    pip install jieba

#### 数据集

数据很随意，没什么格式要求，像这样的都行

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-25-1024x596.png)

#### 分词

把原始数据按行分词，去掉没用的部分

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-26-1024x388.png)

分词代码如下，可以一次转换单个txt或者整个文件夹（文件夹内不能有其他格式文件）

#coding=utf-8
import re
import jieba as jb
import os
def stopwordslist(filepath):
    stopwords = [line.strip() for line in open(filepath, 'r', encoding='utf-8').readlines()]
    return stopwords

# 对句子进行分词
```python
def seg_sentence(sentence):
    sentence = re.sub(u'[0-9\\.]+', u'', sentence)
    '''jb.add_word('光线摄影学院')# 这里是加入用户自定义的词来补充jieba词典。
    jb.add_word('曾兰老师')# 同样，如果你想删除哪个特定的未登录词，就先把它加上然后放进停用词表里。
    jb.add_word('网页链接')
    jb.add_word('微博视频')'''
    sentence_seged = jb.cut(sentence.strip())
    stopwords = stopwordslist('stopwords.txt')  # 这里加载停用词的路径
    outstr = ''
    for word in sentence_seged:
        if word not in stopwords and word.__len__()>1:
            if word != '\\t':
                outstr += word
                outstr += " "
    return outstr

def seg_file(input_file,output_file):#单个txt
    inputs = open(input_file, 'r', encoding='utf-8')
    outputs = open(output_file, 'w',encoding='utf-8')
    for line in inputs:
        line_seg = seg_sentence(line)  # 这里的返回值是字符串
        outputs.write(line_seg + '\\n')
    outputs.close()
    inputs.close()

def seg_dir(input_dir,output_dir):#整个文件夹
    filelist=os.listdir(input_dir)
    for files in filelist:
        inputs = open(input_dir+'/'+files, 'r', encoding='utf-8')
        outputs = open(output_dir+'/'+files, 'w',encoding='utf-8')
        for line in inputs:
            line_seg = seg_sentence(line)  # 这里的返回值是字符串
            outputs.write(line_seg + '\\n')
        outputs.close()
        inputs.close()


seg_dir("original_input","processed_input")
#seg_file("original_input/绍兴.txt","processed_input/绍兴.txt")
```
#### 聚类

相关性强的词聚为一类

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-27.png)

聚类代码如下
```python
#coding=utf-8
import codecs
import os
from gensim import corpora
from gensim.models import LdaModel
from gensim.corpora import Dictionary

def classify(file):
    train = []
    fp = codecs.open('processed_input/'+file,'r',encoding='utf8')
    for line in fp:
        if line != '':
            line = line.split()
            train.append([w for w in line])

    dictionary = corpora.Dictionary(train)

    corpus = [dictionary.doc2bow(text) for text in train]

    lda = LdaModel(corpus=corpus, id2word=dictionary, num_topics=5, passes=100)
    # num_topics：主题数目
    # passes：训练伦次
    # num_words：每个主题下输出的term的数目

    if os.path.exists('txt_output/'+file):
        os.remove('txt_output/'+file)
    f=open('txt_output/'+file,'w',encoding='utf-8')
    for topic in lda.print_topics(num_words = 10):
        termNumber = topic[0]
        print(topic[0], ':', sep='')
        f.write(str(topic[0])+':\\n')#输出保存到文件
        listOfTerms = topic[1].split('+')
        for term in listOfTerms:
            listItems = term.split('*')
            result='  '+listItems[1]+'('+str(listItems[0])+')'
            print(result)
            f.write(result+'\\n')#输出保存到文件
    f.close()
classify('塞纳河.txt')
```
#### 可视化

添加依赖

    pip install pyldavis

在聚类代码的基础上，增加可视化，保存网页文件到本地
```python
#coding=utf-8
import codecs
import os

import gensim
import OpenSSL.crypto
import pyLDAvis.gensim_models
from gensim import corpora, models
from gensim.corpora import Dictionary
from gensim.models import LdaModel


def visualize(file):
    train = []
    fp = codecs.open('processed_input/'+file,'r',encoding='utf8')
    for line in fp:
        if line != '':
            line = line.split()
            train.append([w for w in line])

    dictionary = corpora.Dictionary(train)

    corpus = [dictionary.doc2bow(text) for text in train]

    lda = LdaModel(corpus=corpus, id2word=dictionary, num_topics=5, passes=100)
    # num_topics：主题数目
    # passes：训练伦次
    # num_words：每个主题下输出的term的数目

    if os.path.exists('txt_output/'+file):
        os.remove('txt_output/'+file)
    f=open('txt_output/'+file,'w',encoding='utf-8')
    for topic in lda.print_topics(num_words = 10):
        termNumber = topic[0]
        print(topic[0], ':', sep='')
        f.write(str(topic[0])+':\\n')#输出保存到文件
        listOfTerms = topic[1].split('+')
        for term in listOfTerms:
            listItems = term.split('*')
            result='  '+listItems[1]+'('+str(listItems[0])+')'
            print(result)
            f.write(result+'\\n')#输出保存到文件
    f.close()
    #dictionary = gensim.corpora.Dictionary.load('lda.dict')
    #corpus = gensim.corpora.MmCorpus('lda.mm')
    #lda = models.ldamodel.LdaModel.load('lda.lda')
    vis = pyLDAvis.gensim_models.prepare(lda, corpus, dictionary)
    pyLDAvis.save_html(vis, 'html_output/'+file[:-4]+'.html')

visualize("绍兴.txt")
```
![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-28-1024x646.png)

#### 可视化结果的含义

左边的每个气泡代表一个主题，气泡越大，序号也越小，代表这个主题出现的频率越高，气泡之间的距离远近代表主题之间的相关性，气泡重叠代表主题之间的特征词有交叉重复

选中主题气泡后，右侧列出了这个主题下的30个最具相关性的主题词，红色柱子代表该特征词在这个主题下出现的次数，更长的浅蓝色柱子代表该主题词在整个文本中出现的次数

右上角的λ参数，可以通过拉动调节，代表的是主题词与主题的相关性。如果λ接近1，那么在该主题下更频繁出现的词，跟主题更相关；如果λ越接近0，那么该主题下更特殊、更独有的词，跟主题更相关。

#### 备注

目录格式如下

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-29.png)