---
title: A Beautiful Hexo Theme with Cover Photo Design
date: '2014-02-13'
author: Daisy Gao
tags: [Hexo, Hexo theme]
categories: [Projects]
thumbnailImagePosition: right
thumbnailImage: http://ww2.sinaimg.cn/large/6cea169fjw1edhzrl42srj21400jnk2r.jpg
---

I created a chic Hexo theme "Cover" with a facebook-like cover photo -- [hexo-themes-cover](https://github.com/daisygao/hexo-themes-cover). It was forked from [writing](https://github.com/yunlzheng/hexo-themes-writing).

## Demo

[demo site](http://daisygao.me)

<!--more-->

{{< figure src="http://ww2.sinaimg.cn/large/6cea169fjw1edhzrl42srj21400jnk2r.jpg" title="Index page - cover, with social site links integrated" >}}
{{< figure src="http://ww1.sinaimg.cn/large/6cea169fjw1edhzsngsyjj213l0jmtbu.jpg" title="Index page - blog" >}}
{{< figure src="http://ww1.sinaimg.cn/large/6cea169fjw1edhzude2koj21400jn40v.jpg" title="Post page, with pagination, share module and comment module integrated" >}}

## Features
  - Dynamically-resized facebook-like cover photo displayed on index page, backed by [Anystretch](https://github.com/danmillar/jquery-anystretch).  
  - Mobile-optimized by using Twitter bootstrap.
  - Dynamically-fixed nav-bar design.
  - Beautiful profile design with logo, social site links.
  - Duoshuo comment widget and Jiathis share widget integrated (CSS-hack involved!), friendly for Chinese users.
  - Newer and older posts pagination support.

## Install

Execute the following command and modify `theme` in `_config.yml` to `cover`.

```
cd your-hexo-dir
git clone https://github.com/daisygao/hexo-themes-cover.git themes/cover
```

## Update

Execute the following command to update new.

```
cd themes/cover
git pull
```

## Config

Default config:

```
menu:
  Home: /
  Archives: /archives

widgets:
- search

cover:
  enable: true
  url: http://ww1.sinaimg.cn/large/6cea169fjw1edgyzma1xcj21kw16ohba.jpg
   
excerpt_link: Read More

full_format: 'ddd, MMM D YYYY, h:mm:ss a'

addthis:
  enable: true
  pubid:
  facebook:
  twitter:
  google: true
  pinterest:

fancybox: true

google_analytics: UA-36877105-X
rss:

comment_provider: duoshuo


# Duoshuo comment
duoshuo:
  short_name: your_name

social:
  github: https://github.com/your_name
  weibo: http://weibo.com/your_name


auto_change:
  enable: true

```
