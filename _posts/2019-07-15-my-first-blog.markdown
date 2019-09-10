---
layout: post
title:  My first blog
date:   2019-07-15 22:17:20 +0800
description: I have finally finished buliding my website,WOW! # Add post description (optional)
img: sun_behind_the_mountain.jpg # Add image post (optional)
tags: [Blog, Mountains , jekyll]
author: Walter White # Add name author (optional)
---
<style type="text/css">
p{ line-height: 170% }
</style>

>### 我的博客终于搭建完毕了，其中遇到了无数的问题，不过结果依旧开心，可喜可贺，可喜可贺！

<br>
<br>
想想从以下几个方面总结一下博客的搭建吧：

1. [Github创建项目与展示](#title1)
2. [Jekyll安装配置与使用](#title2)
3. [遇到的问题与解决办法](#title3)
4. [感谢](#title4)


<span id="title1"></span>
### 一. Github创建项目与展示

&emsp;&emsp;首先，github为用户提供了一个终身免费的域名: username.github.io，可以利用这个域名搭建自己的博客  
&emsp;&emsp;其次，github下的repositories也可以展示为网页。方法是点击要展示的repository->Settings->GitHub Pages->Source下选择master branch。  
&emsp;&emsp;由于github.io是基于jekyll的，所以接下来我们需要学习如何用jekyll搭建一个网站

<span id="title2"></span>
### 二. Jekyll安装配置与使用

1.Jekyll的安装配置

&emsp;&emsp;基本可以参考Jekyll的官方网站 https://jekyllrb.com/ 其对应的中文网站 http://jekyllcn.com/ （但是这个中文网站的版本落后于英文官网，从其右侧布局可以看出，但依然很有参考价值）还有一个中文网站不做推荐，因为翻译不太完整 https://www.jekyll.com.cn/docs/

&emsp;&emsp;安装Jekyll 因为Jekyll is a Ruby Gem。所以需要依次安装Ruby,Jekyll和bundler

    sudo apt install ruby-full
    gem install jekyll bundler

&emsp;&emsp;然后我们可以开始新建一个Jekyll博客，或者FORK一个优秀的模板。FORK时需要注意对方Gemfile里的plugins,使用`bundle install`来安装所有需要的plugins，然后`bundle exec jekyll serve`即可开启服务，如需开启多个jekyll serve 可加`--port xxxx`指定端口

2.Jekyll的使用

&emsp;&emsp;使用具体可以参考官网的教程。我这里说说我遇到的几个问题。首先我是想用这github.io搭建一个类似与导航的首页，其次可以兼顾BLOG的功能。这就牵扯到一个问题，jekyll的分页只支持在index.html下进行，如果想用网址访问首页（不加\xxx）则BLOG主页无法完成分页展示所有BOLG的功能。所以当时我的第一考虑是再搭建一个repository，这样BLOG使用github.io/blog。但是发现这样会出现一个bug，点击下一页，出现404。这个问题困扰了我很久，后来经过多次google，并再次翻阅官方文档，发现：
> 分页功能从名为 index.html 的 HTML 文件中被调用时，才能工作

也就是只要名为index.html就可以，所以我新建了一个blog文件夹，在其下放入index.html。这样依旧可以实现在对_post/内容的分页。

<span id="title3"></span>
### 三. 遇到的问题与解决办法

1.在移动设备上滑动没有惯性

&emsp;&emsp;在搭建完毕后发现在pad或者iphone上浏览此网页滑动非常生涩，没有惯性。之后google后发现是解决此问题需要在css中添加`webkit-overflow-scrolling: touch;`。由于此模板是fork的<a href="https://github.com/artemsheludko/adam-blog">adam-blog</a>的，不知道在哪个css模块添加合适，于是我就访问了adam-blog的github.io。但让我惊讶的是，他的网页滑动有惯性！那我就开始怀疑是我更改的哪个位置的代码产生了这个问题，于是我将自己fork的adam-blog的代码和我自己更改后的代码做对比，搜索touch，并没有搜到结果，也有发现问题。这个问题让我非常头大，耗费了近一个小时。最后在调试彼此的网页后发现，adam已经添加了`webkit-overflow-scrolling: touch;`。我错在是拿自己两年前fork的代码和自己更改的代码做的对比，这个BUG在去年修复的.......

&emsp;&emsp;于是我又仔细浏览了adam最近做的修改，并应用的自己的网站上。总结:

>如果是fork的别人的repo，要经常查看原作者做的改动！！！

ps:使用chrome调试可以看到touch属性，但是用电脑chrome的移动设备模式调试，无法展现出touch的惯性
![截图]({{site.baseurl}}/assets/img/touch.png)

2.在局域网内通过其他终端访问jekyll

&emsp;&emsp;由于jekyll将地址绑定到了127.0.0.1，导致局域网的其它机器并不能访问它的服务。但实际上只要改变运行jekyll的参数就可以了。

`$ jekyll serve -w --host=0.0.0.0`

<span id="title4"></span>
### 四.感谢

&emsp;&emsp;首先感谢adam，因为模板是fork自<a href="https://github.com/artemsheludko/adam-blog">adam-blog</a>的。感谢欢，是她一直陪伴着我，从搭建这个网站到这篇博客，感谢她时刻给予我的鼓励。感谢平，是他的一句玩笑话“你的网站毫无ui”，因此，我才将原来一个html扩展到现在的主页+博客。

