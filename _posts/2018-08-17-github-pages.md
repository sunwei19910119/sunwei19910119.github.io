---
layout: post
title:  "使用GitHub Pages建立博客"
date:   2018-08-17
desc: "使用GitHub Pages建立博客"
keywords: "Jekyll,gh-pages,website,blog,easy"
categories: [Others]
tags: [github,Jekyll]
icon: icon-html
---

## 简介
Github很好的将代码和社区联系在了一起，于是发生了很多有趣的事情，世界也因为他美好了一点点。Github作为现在最流行的代码仓库，已经得到很多大公司和项目的青睐，比如jQuery、Twitter等。为使项目更方便的被人理解，介绍页面少不了，甚至会需要完整的文档站，Github替你想到了这一点，他提供了Github Pages的服务，不仅可以方便的为项目建立介绍站点，也可以用来建立个人博客。

Github Pages有以下几个优点：   
 * 轻量级的博客系统，没有麻烦的配置   
 * 使用标记语言，比如Markdown   
 * 无需自己搭建服务器   
 * 根据Github的限制，对应的每个站有300MB空间   
 * 可以绑定自己的域名

当然他也有缺点：

* 使用Jekyll模板系统，相当于静态页发布，适合博客，文档介绍等。   
* 动态程序的部分相当局限，比如没有评论，不过还好我们有解决方案。   
* 基于Git，很多东西需要动手，不像Wordpress有强大的后台   

大致介绍到此，作为个人博客来说，简洁清爽的表达自己的工作、心得，就已达目标，所以Github Pages是我认为此需求最完美的解决方案了。     

## 购买、绑定独立域名
选域名，注册，解析到：185.199.111.153。地址可能发生变动，参考[这里](https://help.github.com/articles/troubleshooting-custom-domains/)

## 配置GitHub
[Git中文教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

## 使用GitHub Pages建立博客
与GitHub建立好链接之后，就可以方便的使用它提供的Pages服务，GitHub Pages分两种，一种是你的GitHub用户名建立的`username.github.io`这样的用户&组织页（站），另一种是依附项目的pages。
进入项目设置，绑定域名

## 搭建本地jekyll环境
这里主要介绍一下在Mac OS X下面的安装过程，其他操作系统可以参考官方的[jekyll安装](https://github.com/mojombo/jekyll/wiki/Install)。

作为生活在水深火热的墙内人民，有必要进行下面一步修改gem的源，方便我们更快的下载所需组建：
```
sudo gem sources --remove http://rubygems.org/
sudo gem sources -a http://ruby.taobao.org/
```
然后用Gem安装jekyll
```
sudo gem install jekyll
```
我到了这一步的时候总是提示错误`Failed to build gem native extension`，很可能的一个原因是没有安装rvm，rvm的安装可以参考这里，或者敲入下面的命令：
```
curl -L https://get.rvm.io | bash -s stable --ruby
```
好了，如果一切顺利的话，本地环境就基本搭建完成了，进入之前我们建立的博客目录，运行下面的命令：
```
jekyll serve --watch
```
这个时候，你就可以通过`localhost:4000`来访问了




