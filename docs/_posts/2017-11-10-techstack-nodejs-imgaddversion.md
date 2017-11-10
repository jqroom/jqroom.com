---
layout: post
title: 给文件里面引入的图片增加时间戳
date: '2017-11-10 18:31'
categories: techstack
permalink: /techstack/nodejs/20171110/imgaddversion
---

# 给文件里面引入的图片增加时间戳

> nodejs小工具，解决重构提供的样式里面引入的图片的缓存问题

```
let file = process.argv[2];
let fs = require('fs');
let path = require('path');
let exec = require('child_process').exec;
let str = fs.readFileSync(file);

let resultStr = str.toString().replace(/(\.png|\.jpg)(\s|\)|\'|\")/g, '$1?v=' + new Date().getTime() + '$2');

fs.writeFileSync(file, resultStr);
```

# 使用

`node build.js /Users/aa/git/github/blog/bb.css`
