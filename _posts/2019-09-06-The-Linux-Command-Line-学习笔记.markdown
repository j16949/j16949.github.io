---
layout: post
title:  The Linux Command Line 学习笔记
date:   2019-09-06 19:17:20 +0800
description: The Linux Command Line 学习笔记 # Add post description (optional)
img: post-5.jpg # Add image post (optional)
tags: [Blog, Linux , bash]
author: Walter White # Add name author (optional)
---
<style type="text/css">
p{ line-height: 170% }
</style>

>### 记录学习《The Linux Command Line》中的心得与遇到的问题。

<br>
<br>
导航：

1. [心得](#title1)
2. [学习笔记](#title2)
3. [关于书中的例子](#title3)



<span id="title1"></span>
### 一. 心得

想当时突发奇想想学一学Linux,网上看了不少推荐教材。看大家推荐不少好书，其中就有《The Linux Command Line》。读起来发现这本书不是那种特别学术和格式化的教学，而是一步步带你操作和深入，像读故事一样轻松愉快。同时也可以将这本书当作一本工具书作为查询使用。

<span id="title2"></span>
### 二. 学习笔记




<span id="title3"></span>
### 三. 关于书中的例子

**1.here document**

不管是PDF书还是网页版的TCL <http://billie66.github.io/TLCL/book/> 。当复制例子时，都会出现空白字符用空格而不是TAB填充。而重定向操作符从“<<”改为“<<-”,shell 会忽略在此 here document 中开头的 tab 字符（只忽视TAB字符，没有空格)。所以我们必须把空格改为TAB。这里有两种办法：

**a.在有'<<'或‘<<-'出现的段落的首尾用TAB替换空格,如:**
{% highlight shell %}

bai@ubuntu:~/myshell/bcProject$ cat -A test2
#!/bin/bash$
# loan-calc : script to calculate monthly loan payments$
PROGNAME=$(basename $0)$
usage () {$
    cat <<- EOF$
    Usage: $PROGNAME PRINCIPAL INTEREST MONTHS$
    Where:$
    PRINCIPAL is the amount of the loan.$
    INTEREST is the APR as a number (7% = 0.07).$
    MONTHS is the length of the loan's term.$
^IEOF$
}$


{% endhighlight %}

**b.全局用TAB替换4个空格`:%s\    \^I\g`**


