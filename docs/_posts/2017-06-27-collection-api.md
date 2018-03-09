---
layout: post
title:  "知识收集"
date:   2018-03-02 16:58
categories: collection
permalink: /collection/20170627/knowledge
---

### 知识收集

-   [从输入URL到页面展示到底发生了什么](http://web.jobbole.com/91239/)

-   [详解JavaScript中的Event Loop（事件循环）机制](https://zhuanlan.zhihu.com/p/33058983)

-   [Web前端应该从哪些方面来优化网站](https://www.zhihu.com/question/21658448)

-   [HTTP 缓存](https://developers.google.cn/web/fundamentals/performance/optimizing-content-efficiency/http-caching)

-   [了解 Resource Timing](https://developers.google.cn/web/tools/chrome-devtools/network-performance/understanding-resource-timing)

### 遇到过的问题

-   如何计算页面白屏时间？
-   vue双向绑定原理，以及和angular的区别([剖析Vue原理&实现双向绑定MVVM](https://segmentfault.com/a/1190000006599500))
    > AngularJS 采用“脏值检测”的方式，数据发生变更后，对于所有的数据和视图的绑定关系进行一次检测，识别是否有数据发生了改变，有变化进行处理，可能进一步引发其他数据的改变，所以这个过程可能会循环几次，一直到不再有数据变化发生后，将变更的数据发送到视图，更新页面展现。如果是手动对 ViewModel 的数据进行变更，为确保变更同步到视图，需要手动触发一次“脏值检测”。
    >
    > VueJS 则使用 ES5 提供的 Object.defineProperty() 方法，监控对数据的操作，从而可以自动触发数据同步。并且，由于是在不同的数据上触发同步，可以精确的将变更发送给绑定的视图，而不是对所有的数据都执行一次检测。
