---
layout: post
title:  "js and browser"
date:   2018-04-27 17:30
categories: techstack
permalink: /techstack/20180427/js-and-browser
---

js和浏览器，总有说不完的事情。

### 个人查阅资料后总结的观点：
- 浏览器加载html是“自上而下”的加载
- 外链script标签增加async之后，js下载与浏览器渲染页面是同时进行的，但是一旦js下载完毕，渲染便会停止，等待js加载、解析、执行完成之后，才会接着渲染。
