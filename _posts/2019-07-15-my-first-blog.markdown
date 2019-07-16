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
1. Github创建项目与展示
2. Jekyll安装配置与使用
3. 遇到的问题与解决办法
4. 感谢

### 一. Github创建项目与展示

&emsp;&emsp;首先，github为用户提供了一个终身免费的域名: username.github.io，可以利用这个域名搭建自己的博客  
&emsp;&emsp;其次，github下的repositories也可以展示为网页。方法是点击要展示的repository->Settings->GitHub Pages->Source下选择master branch。  
&emsp;&emsp;由于github.io是基于jekyll的，所以接下来我们需要学习如何用jekyll搭建一个网站

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