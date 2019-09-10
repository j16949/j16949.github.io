---
layout: post
title:  Linux bash vim code 个人使用技巧总结
date:   2019-07-31 10:17:20 +0800
description: I have finally finished buliding my website,WOW! # Add post description (optional)
img: wordcloud.jpg # Add image post (optional)
tags: [Blog, Linux , bash , vim , code]
author: Walter White # Add name author (optional)
---
<style type="text/css">
p{ line-height: 170% }
</style>

>### 工欲善其事，必先利其器。本文总结一下平时bash和vim等使用的技巧吧。

<br>
<br>
导航：

1. [复制文件或内容](#title1)
2. [实用快捷键](#title2)
3. [关于本篇博客](#title3)


<span id="title1"></span>
### 一. 复制文件或内容

1.复制文件

- `cp fileA fileB`

- `vim fileB`,然后输入ESC+':',`r fileA`(即把fileA内容read到fileB)

- `vim -o fileA fileB`（同时编辑两个文件) ,y+G全部复制,ctrl+w,按方向键选择fileA或fileB,p粘贴

2.复制内容

- `vim fileA`,按v进入visual模式，移动方向键，然后y复制

- y+W复制单词，y+$复制到行尾，y+G复制到文件末尾


<span id="title2"></span>
### 二. 实用快捷键

1.Ctrl+d 选择下一个相同元素(code,sublime可用)



<span id="title3"></span>
### 三.关于本篇博客

可以看到最上方的图片使用了词云,代码如下：

{% highlight python %}

#wordCloud.py
import jieba
import wordcloud
from  imageio import imread
#中文
'''mask=imread('fivestart.jpg')
f=open('新时代中国特色社会主义.txt','r',encoding='utf-8')
t=f.read()
f.close()
ls=jieba.lcut(t)
txt=' '.join(ls)
w=wordcloud.WordCloud(font_path='msyh.ttc',max_words=99,mask=mask,background_color='white')
w.generate(txt)
w.to_file('grwordcloud.png')'''

#英文
mask=imread('sun.jpg')
#f=open('words.txt','r')
#t=f.read()
#f.close()
t='Linux,bash,vim,code'
w=wordcloud.WordCloud(max_words=99,mask=mask,background_color='white',collocations=True,repeat=True)
w.generate(t)
w.to_file('wordcloud.jpg')

{% endhighlight %}

>!!!如果想让一个词重复多次，需要设置 `wordcloud.WordCloud(repeat=True)` !!!

