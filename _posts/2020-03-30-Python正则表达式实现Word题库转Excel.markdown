---
layout: post
title:  Python正则表达式实现Word题库转Excel
date:   2020-03-30 21:21:00 +0800
description: 利用Python正则表达式，匹配现存Word题库中的题目并转为Excel # Add post description (optional)
img: post-7.jpg # Add image post (optional)
tags: [Blog, Python , 正则表达式]
author: Walter White # Add name author (optional)
---
<style type="text/css">
p{ line-height: 170% }
</style>
<!--为表格样式添加边框-->
<style>
	  table{
	    border-left:1px solid #000000;border-top:1px solid #000000;
	    width: 100%;
	    word-wrap:break-word; word-break:break-all;
	  }
	  table th{
	  text-align:center;
	  }
	  table th,td{
	    border-right:1px solid #000000;border-bottom:1px solid #000000;
	  }
</style>
>### 利用Python正则表达式，匹配现存Word题库中的题目并转为Excel。

<br>
<br>
导航：

1. [背景介绍](#title1)
2. [面临的问题](#title2)
3. [单选](#title3)
4. [多选](#title4)
5. [判断](#title5)
6. [简答](#title6)
7. [总结](#title7)



<span id="title1"></span>
### 一. 背景介绍

单位由于新建了一套生产运行系统，原来的题库已经过于老旧，于是要求各个科室整理出一套适用于新系统的题库。单位给出了Excel题库的格式如下

| 题目类别 | 知识点    | 题目类型        | 题目编号     | 题目名称           | 题目答案         | 选项A | 选项B | 选项C | 选项D |
|------|--------|-----------|----------|----------------|--------------|-----|-----|-----|------|
| 应知应会 | 所属系统名称 | 1（单选）     | 自由编号数字类型 | 题干的内容          | 填写答案内容，而不是选项 |     |     |     |      |
|      |        | 2（判断）     |          |                | 填写T或者F       |     |     |     |      |
|      |        | 3（简答）     |          |                | 填写答案         |     |     |     |      |
| 例子如下 |        | 4（多选）     |          |                | 填写答案选项       |     |     |     |      |
| 应知应会 | 自动转报系统 | 1         | 1        | 自动转报系统分为哪几种类型？ | 以上都对         | 答案1 | 答案2 | 答案3 | 答案4 |

很荣幸被领导安排了出将近70个单选，20个多选，20个判断和20个简答。而这些题目其实在新系统建设过程中，我都阶段性的出过，并且有很多之前出的题目可以继续使用。只是他们都是如下所示的Word版本
```
1.我局使用的准入技术是（C）  
A.portal                                      B. 802.11
C.802.1x                                      D.802.1n

2.网线线序常见有T568A，T568B两种标准，他们的线序是（ AB）
A.白橙、橙、白绿、蓝、白蓝、绿、白棕、棕
B.白绿、绿、白橙、蓝、白蓝、橙、白棕、棕
C.白绿、绿、白橙、蓝、白棕、棕、白蓝、橙
D.白棕、棕、白绿、绿、白橙、蓝、白蓝、橙

3.用户可以自由选择自用终端登陆管理信息网络或是互联网。（ X )

4.登录一台华为交换机在端口开关准入配置dot1x
答：登陆交换机→进入系统视图→进入接口视图→dot1x enable(开启准入)；4 undo dot1x enable（关闭准入)
```
那么我要做的就是将这些已经出的题目整理到这个Excel中。由于题目量较大，手动整理工作量较大且枯燥。联想到之前学Python爬虫时，可以将一些存在共性的内容整理到一起。于是利用Python的re正则表达式，匹配出选项和答案，自动整理到Excel题库中。

<span id="title2"></span>
### 二. 面临的问题
由于是不同时间零散出的题目，存在像第单选题1中B选项后带空格的，多选题2中中英文括号混用，判断题3中“T，F，√，X“各种字符的问题。主要如下：  
1. 如何精确匹配一道题目
2. 如何去除多余空格
3. 如何解决中英文符号，特别是中英文括号混用问题
4. 如何解决题目格式不规范，如大小写A，a;选项为`A.``A、`

下面分别展示如何将单选，多选，判断和简答由Word格式转换为Excel,并在转换的过程中解决上述问题。(以下代码运行与Pyhton3.8)


<span id="title3"></span>
### 三. 单选

首先需要说明一下，代码是打开一个TXT文档，将其转换为如上Excel格式文件。这里没有使用WORD，是因为实践中发现`python-docx`这个库是会将WORD中带有项目序号的内容去掉前面的1，2，3等序号，变为一个list，导致python代码中不能准确的取出每到题目。又由于docx本质上是一中XML文件，所以为了简单化处理，还是将其拷贝到TXT中以文本格式引入。

{% highlight python %}

#!/usr/bin/python
#coding:utf-8
import re
import time
import pprint
import pandas as pd
#打开题库
with open('singleChoice.txt','r',encoding='utf8') as f:
    st = f.read()
a = 0
#创建列表保存数据
data=[]
for i in range(1,301):
    try:
        #匹配题目，其中按照“1.*****2.”来匹配一道题目，\s匹配任何空白字符,等价于 [ \f\n\r\t\v]
        s = re.search('(\s' + str(i) + '\..*?)\s' + str(i + 1) + '\.', st, re.S)
        #去掉除空格外的空白字符，包括换行符、回车符、制表符等。
        sf=re.sub('[\n\r\t]','',s.group(1))
        #将题目中的括号答案改为"?",匹配中文或者英文括号，匹配括号中或者括号前后多个空格
        kong = re.sub('( *[\(\（] *[a-fA-F]* *[\)\）])', '?',sf)
        #去掉题目的题号,题号格式为"1."，限制为2位数以内
        kong = re.sub('^[1-9]\d{0,2}\.', '', kong)
        #拆分题目和答案，答案格式为"A、100度"或“A.100”其中选项编号和选项间可含空格
        k=re.split('[A-D] *[、\.] *',kong)
        #取出答案
        key = re.search('( *[\(\（] *[a-fA-F]* *[\)\）])',sf)
        key = re.search('([a-fA-F]+)',key.group(1))
        #将abcd,ABCD转化为1234,赋给cai(correct answer index)
        cai=ord(key.group(1).upper())-63
        #插入题目序号到k数组的第一项
        k.insert(0,i)
		#插入答案到k数组的第二项
        k.insert(2,k[cai])
        #存入data数组
        data.append(k)
    except:
        pass

 #每一列标题
title = ['题号','题目','答案','A','B','C','D']          
test=pd.DataFrame(columns=title,data=data)
test.to_csv('singleChoice.csv',encoding='gbk')

{% endhighlight %}

<span id="title4"></span>
### 四. 多选

单选要求填写答案内容，而多选要求填写答案选项

{% highlight python %}

#!/usr/bin/python
#coding:utf-8
import re
import time
import pprint
import pandas as pd
#打开题库
with open('multiChoice.txt','r',encoding='utf8') as f:
    st = f.read()
#创建列表保存数据
data=[]
for i in range(1,301):
    try:
        #匹配题目，其中按照“1.*****2.”来匹配一道题目，\s匹配任何空白字符,等价于 [ \f\n\r\t\v]
        s = re.search('(\s' + str(i) + '\..*?)\s' + str(i + 1) + '\.', st, re.S)
        #去掉除空格外的空白字符，包括换行符、回车符、制表符等。
        sf=re.sub('[\n\r\t]','',s.group(1))
        #将题目中的括号答案改为"?"
        kong = re.sub('( *[\(\（] *[a-fA-F]* *[\)\）])', '?',sf)
        #去掉题目的题号,题号格式为"1."
        kong = re.sub('^[1-9]\d{0,2}\.', '', kong)
        #拆分题目和答案，答案格式为"A、100度"
        k=re.split('[A-D] *[、\.] *',kong)
        #取出答案
        key = re.search('( *[\(\（] *[a-fA-F]* *[\)\）])',sf)
        key = re.search('([a-fA-F]+)',key.group(1))
        #插入答案到k数组的第二项
        k.insert(0,i)
        k.insert(2,key.group(1))
        #存入data数组
        data.append(k)
        #os.system('CLS')
    except:
        pass

title = ['题号','题目','答案','A','B','C','D']           #每一列标题
test=pd.DataFrame(columns=title,data=data)
print(test)
test.to_csv('multiChoice.csv',encoding='gbk')

{% endhighlight %}

<span id="title5"></span>
### 五. 判断

判断主要在与将“T，F，√，X“替换为统一的表示形式

{% highlight python %}

#!/usr/bin/python
#coding:utf-8
import re
import time
import pprint
import pandas as pd
#打开题库
with open('judge.txt','r',encoding='utf8') as f:
    st = f.read()
#创建列表保存数据
data=[]
for i in range(1,301):
    try:
        #匹配题目
        s = re.search('(\s' + str(i) + '\..*?)\s' + str(i + 1) + '\.', st, re.S)
        #去掉除空格外所有空白字符
        sf=re.sub('[\n\r\t]','',s.group(1))
        #去掉题目的题号,题号格式为"1."
        kong = re.sub('^[1-9]\d{0,2}\.', '', sf)
        #拆分题目和答案，答案格式为（T）等
        k = re.split('( *[\(\（] *[√xtfXTF]+ *[\)\）])',kong)
        #取出答案
        key=re.search('([√xtfXTF])',k[1]).group(1)
        if key=='x' or key=='X' or key=='f' or key=='F':
            k[1]='F'
        else:
            k[1]='T'
        k.insert(0,i)
        del k[3]
        #存入data数组
        data.append(k)
    except:
        pass

#每一列标题
title = ['题号','题目','答案']           
test=pd.DataFrame(columns=title,data=data)
print(test)
test.to_csv('judge.csv',encoding='gbk')

{% endhighlight %}

<span id="title6"></span>
### 六. 简答

简答统一按“答：”来拆分题目

{% highlight python %}

#!/usr/bin/python
#coding:utf-8
import re
import time
import pprint
import pandas as pd
#打开题库
with open('jianda.txt','r',encoding='utf8') as f:
    st = f.read()
#创建列表保存数据
data=[]
for i in range(1,301):
    try:
        #匹配题目
        s = re.search('(\s' + str(i) + '\..*?)\s' + str(i + 1) + '\.', st, re.S)
        #去掉除空格外所有空白字符
        sf=re.sub('[\n\r\t]','',s.group(1))
        #去掉题目的题号,题号格式为"1."
        kong = re.sub('^[1-9]\d{0,2}\.', '', sf)
        #拆分题目和答案，答案格式为"答："
        k = kong.split('答：')
        #插入题目序号
        k.insert(0,i)
        #存入data数组
        data.append(k)
    except:
        pass

#每一列标题
title = ['题号','题目','答案']           
test=pd.DataFrame(columns=title,data=data)
print(test)
test.to_csv('jianda.csv',encoding='gbk')

{% endhighlight %}

<span id="title7"></span>
### 七. 总结

以上通过把题目分为单选，多选，判断和简答，分别用4个Python小程序将4个TXT转换为Excel。基本实现了需求，其中主要靠正则表达式匹配出题目和选项。然而程序还存在很多的不足，比如:

1. `for i in range(1,301):`会固定循环300次，浪费资源
2. 每次`s = re.search（）`都要遍历整个文档，效率低
3. 操作复杂，要将WORD复制到TXT，在转Excel

其实如果经常有出题的需求，应该做一个题库系统，将所有的题目按类别统一录入题库系统的数据库。这样有需求的话，只须将数据库的内容按不同形式表现。同时，更进一步的话可以将题库系统增加答题，错题统计等等的功能。

>**题外话**

用Python实现了题目形式的转化，其实说实话还不如自己一道一道出题，再录入Excel花费的总的时间少。不过生命贵在折腾么。再加上2020年Covid-19爆发，才让自己有时间折腾这么个程序。

博客好久没有更新了，算算距离上次更新已经过去快半年了。其实这一段时间比较迷茫，面试了一些企业发现自己可能并没有机会再进入软件这个圈子。于是想想职业的道路还能往哪个方向发展，工作了这么多年主要负责网络和网络安全，也许在这个方向可以尝试一把。  
于是去年10月报名学习了HCIE R&S，也在今年初学完了第一轮，本想年后开始考试，争取早日拿上证书。然而，因为Covid-19，整个考试也被延后。在学习华为数通的过程中，也在了解网络的发展趋势，即渐渐的向SDN，SDWAN发展。未来的趋势就是控制平面与转发平面的进一步的分离，更合理细致化的流量转发将由软件平面控制。所以这么想，网络、软件还是一家。  
学习的过程也是一个自我了解的过程。我发现自己不管从事哪个行业，哪种工作，总是希望创造一些新颖的东西。寻找事物发展的规律。所以，未来仍可期，不忘初心，方得始终！



