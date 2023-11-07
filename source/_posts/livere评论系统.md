---
title: livere评论系统
categories:
  - 线上评论
tags:
  - livere
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: d318877c
date: 2022-04-27 09:21:11
pic:
---

### Hexo博客添加LiveRe评论系统

好久不见,又是长长的探索实践之旅了,最近在新加评论系统出现了各种问题,也有所得.
评论系统可以说五花八门啦，用得多的比如：Gitment、Gitalk、LiveRe、Disqus、disqusjs，twikoo, waline、Valine 等
<!--more-->
##### 这里以LiveRe为例:
1.进入来必力官网 [https://www.livere.com/](https://www.livere.com/)
2.右上角个人头像点进去 有账号可以直接登录
3.没有就注册一个[https://was.livere.me/register?lang=zh-cn](https://was.livere.me/register?lang=zh-cn),填写自己的邮箱,我这里用了网易163邮箱,可以根据自己情况填写,填写密码完成注册.
4.注册以后登录进去，点击导航栏安装,选择City版进行安装，City版是免费的，对我们这种个人博客而言完全足够了
![](1.jpg)
5.接下来需要填写一些关于你想将LiveRe用于的博客的一些信息
6.填完之后，申请获取代码，此时其将会给你一段代码，该段代码等下需要加到你的个人博客的页面中，我们可以先将其复制并保存起来。
### LiveRe评论配置集成
1.回到我们的博客根目录打开themes主题文件夹 我用的是kratos-rebirth主题,以kratos-rebirth为例:
![](2.jpg)
打开_config.yml文件更改配置
![](3.jpg)
我们更改Post Page 文章页配置posts下属性comments的属性取值
这里用到的评论系统是livere 对应更改provider: livere
enableBGImage用来配置背景图片
2.找到\kratos-rebirth\layout\_comments文件夹 更改 livere.ejs文件配置 没有可以创建一个
3.打开livere文件,新增来必力评论代码如下:
```bash
<div id="lv-container" class="post-comments lazy-load <% if (theme.posts.comments?.enableBGImage) { %> bg-image<% } %>" data-id="<%- theme.livere.id || 'city' %>" data-uid="<%- theme.livere.uid %>">
<script type="text/javascript">
   (function(d, s) {
       var j, e = d.getElementsByTagName(s)[0];

       if (typeof LivereTower === 'function') { return; }

       j = d.createElement(s);
       j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
       j.async = true;

       e.parentNode.insertBefore(j, e);
   })(document, 'script');
</script>
<noscript>为正常使用来必力评论功能请激活JavaScript</noscript>
</div>
<!-- City版安装代码已完成 -->
```
老样子,先来三杯小鸟伏特加 powershell或命令行打开博客根目录
键入命令:
```bash
hexo cl
hexo g
hexo s
```
本地部署完成后可以打开博客文章访问就可以看到文章下方加载除评论区了,选择账号登录就可以进行评论了.
确认无误hexo d部署到服务器上
[https://drfengling.github.io](https://drfengling.github.io)
由于研究了多种博客评论系统 所以你看到的评论系统不一定是livere.有问题评论区欢迎评论交流.
到此,livere评论系统就完成啦，快来动手试试吧.
