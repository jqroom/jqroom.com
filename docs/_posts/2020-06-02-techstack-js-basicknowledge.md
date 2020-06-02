---
layout: post
title:  "js basic knowledge collection"
date:   2020-06-02 20:20
categories: techstack
permalink: /techstack/20200602/js-basic-knowledge
---

我的网络记事本，主要记录前端相关的一些容易忽略或遗忘的知识点。

### javascript比较操作符类型转换规则
1. 如果两个操作值都是数值，则进行数值比较
1. 如果两个操作值都是字符串，则比较字符串对应的字符编码值
1. 如果只有一个操作值是数值，则将另一个操作值转换为数值，进行数值比较
1. 如果一个操作值是布尔值，则将其转换为数值，再进行比较
1. 如果一个操作数是对象，则调用 valueOf()方法（如果对象没有 valueOf()方法则调用 toString()方法），得到的结果按照前面的规则执行比较
> 注：NaN 是非常特殊的值，它不和任何类型的值相等，包括它自己，同时它与任何类型的值比较大小时都返回 false。
