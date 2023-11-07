---
title: Hexo博客添加Gitalk评论
categories:
  - 线上评论
tags:
  - Gitalk
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: 212a5789
date: 2022-04-27 11:09:16
pic:
---

Gitalk 是一个基于 GitHub Issue 和 Preact 开发的评论插件。
更详细用法看官网[https://github.com/gitalk/gitalk/blob/master/readme-cn.md](https://github.com/gitalk/gitalk/blob/master/readme-cn.md)这里只记录有诱惑的操作
<!--more-->
#### 1.申请秘钥

申请一个 OAuth application [https://github.com/settings/applications/new](https://github.com/settings/applications/new)
![](https://gitee.com/lucas27/pic-bed/raw/master/picture/新建应用.jpg)
获取 ID 和 secrets，必须要保存好，然后以后就看不到了，忘记了只能重建。
上面的填写成功之后进入,我们需要的是生成的Client ID 和Client Secret
![](1.jpg)
#### 2.存储仓库

您需要选择一个公共github存储库（已存在或创建一个新的github存储库）用于存储评论
![](2.jpg)
如上所示,我新建了一个dryunment用来存储线上评论.如果没有可以点New新建一个仓库

#### 3.博客配置

上面的确认无误后，我们进行配置博客，引入gitalk。

  首先进入主题的配置文件_config.yml(注意是主题的配置文件，不是博客根目录下的配置文件)，添加gitalk配置(一定要仔细，缺一不可)。
```bash
gitalk:
  owner: github用户名
  admin: github用户名
  repo: 博客的仓库名称(注意不是地址)
  ClientID: 上面生成的Client ID
  ClientSecret: 上面生成的Client Secret
```
上面配置完之后,进入主题目录\themes\kratos-rebirth\layout\_comments目录，在当前目录下新建一个gitalk.ejs文件，写入如下代码
```bash
<link rel="stylesheet" href="<%- url_npm_cdn('gitalk@1/dist/gitalk.css') %>">
<div id="gitalk-container" class="post-comments lazy-load<% if (theme.posts.comments?.enableBGImage) { %> bg-image<% } %>" style="padding-left:2rem; padding-right:2rem;"></div>
<script>
    var load_comm = () => {
        const init = () => {
            console.log('Gitalk loading...');
            const gitalk = new Gitalk(Object.assign({
                id: '<%- url_for(page.path) %>',
                path: '<%- url_for(page.path) %>'
            }, JSON.parse('<%- JSON.stringify(theme.gitalk) %>')));

            gitalk.render('gitalk-container');
        }
        if (typeof Gitalk === 'undefined') {
            const src = '<%- url_npm_cdn("gitalk@1/dist/gitalk.min.js") %>';
            $.getScript(src, init);
        } else {
            init();
        }
    };
load_comm();
</script>
<noscript>Please enable JavaScript to view the <a href="https://github.com/gitalk/gitalk">comments powered by Gitalk.</a></noscript>
```
#### 4.博客部署
键入命令:
```bash
hexo cl
hexo g
hexo s
```
本地部署完成后可以打开博客文章访问就可以看到文章下方加载除评论区了,选择账号登录就可以进行评论了.
确认无误hexo d部署到服务器上
[https://drfengling.github.io](https://drfengling.github.io)
由于研究了多种博客评论系统 所以你看到的评论系统不一定是Gitalk.有问题评论区欢迎评论交流.
到此,Gitalk评论系统就完成啦，快来动手试试吧.
