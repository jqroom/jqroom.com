---
layout: post
title: "Array.prototype.reduce()"
date: '2017-07-14 10:10'
categories: techstack
permalink: /techstack/js/20170714/reduce
---

### 常见面试题，计算出一个字符串里面每个字符出现的次数。

```javascript
var str = 'gangiohZgniaiogioagubaiohagbioshdfahgiooihz';
str.split('').reduce((prev, cur) => (prev[cur] ? prev[cur]++ : prev[cur] = 1, prev), {});
// 控制台输出
// { Z : 1, a : 6, b : 2, d : 1, f : 1, g : 7, h : 5, i : 8, n : 2, o : 7, s : 1, u : 1, z : 1 }
// 如果最后一个初始值是[],则输出结果为：
// [Z : 1, a : 6, b : 2, d : 1, f : 1, g : 7, h : 5, i : 8, n : 2, o : 7, s : 1, u : 1, z : 1]
```

> reduce的第一个参数是一个function(a, b), 第二个参数是一个初始值。对于function，第一个参数在有默认值的时候取默认值，没有默认值的时候取数组的第一个值，最后被function return之后的值重新赋值。

伪代码如下：

```javascript
[1,2,3].reduce(function(a, b){return c;}, d);
// 如果d存在 a = d, b = 1; a = c;
// 如果d不存在 a = 1, b = 2; a = c;
```

### 参考

[Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
