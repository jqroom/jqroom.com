---
layout: post
title: JavaScript event 事件三阶段 target currentTarget
date: '2018-02-27 11:19'
categories: techstack
permalink: /techstack/js/20180227/event
---

# 事件三阶段

JavaScript event 事件三阶段: 捕获阶段、目标阶段、冒泡阶段。

# target和currentTarget的区别

```javascript
document.body.addEventListener('click', function(event){
    // event.target 是当前点击的元素
    // event.currentTarget 是当前事件绑定在的那个元素，此时是body元素
    }, false);
```

> 第三个参数是指在DOM树中，注册了该listener的元素，是否会先于它下方的任何事件目标，接收到该事件。若为true，则是以捕获阶段的顺序执行绑定的事件；若为false，则是以冒泡阶段的顺序执行绑定的事件，同一个元素上，先绑定的事件先执行

# 参考

- [浏览器事件模型中捕获阶段、目标阶段、冒泡阶段实例详解](https://segmentfault.com/a/1190000003482372)
- [MDN eventTarget.addEventListener](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)
- [MDN event.target](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/target)
- [MDN event.currentTarget](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/currentTarget)
