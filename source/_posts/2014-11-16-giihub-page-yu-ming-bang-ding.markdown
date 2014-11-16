---
layout: post
title: "Giihub Page 域名绑定"
date: 2014-11-16 22:34:39 +0800
comments: true
categories: [github]
---
- 在Octopress/source目录下建个无后缀的CNAME文本文件，里面写上域名，如ryvius.me
- 去域名管理网站修改:

  CNAME: www.ryvius.me => cpsa3.github.com

  A: ryvius.me => 207.97.227.245
- 等待域名解析生效