---
layout: post
title:  "var与let的经典闭包问题详解"
date:   2018-05-17 20:20
categories: techstack
permalink: /techstack/20180517/closure
---

在计算机科学中，**闭包（英语：Closure），又称词法闭包（Lexical Closure）或函数闭包（function closures），是引用了自由变量的函数**。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。

### 经典问题

-   **var for循环**

```javascript
var len = 5;
for(var i = 0; i < len; i++){
    setTimeout(() => {
        console.log(i);
    }, 1000 * i);
}
```

> 以前看到有人说：闭包就是函数里面的函数。在这个例子里面只有一个函数，并没有构成函数里面的函数，因此这种说法是不对的。

**控制台输出**

![]({{ site.url }}/images/posts/bibao.gif)
![]({{ site.url }}/images/posts/bibao8.png)
![]({{ site.url }}/images/posts/bibao9.png)

> var声明变量会发生“变量提升”现象，并且变量i是存在于global作用域中。

阮一峰老师是这样解释的：上面代码中，变量i是var命令声明的，在全局范围内都有效，所以全局只有一个变量i。每一次循环，变量i的值都会发生改变，而循环内被赋给数组a的函数内部的console.log(i)，里面的i指向的就是全局的i。也就是说，所有数组a的成员里面的i，指向的都是同一个i，导致运行时输出的是最后一轮的i的值，也就是 5。

-   **let for循环**

```javascript
var len = 5;
for(let i = 0; i < len; i++){
    setTimeout(() => {
        console.log(i);
    }, 1000 * i);
}
```

**控制台输出**

![]({{ site.url }}/images/posts/bibao3.gif)
![]({{ site.url }}/images/posts/bibao6.png)
![]({{ site.url }}/images/posts/bibao7.png)

> {}内没用重新定义i，但是可以访问到i，说明i只可能是{}的父集作用域里定义的变量，但是每次i++之后，{}访问的又不是同一个i，因为如果i是同一个，最后输出的效果就和var的方式一样了。

阮一峰老师是这样解释的：上面代码中，变量i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量，所以最后输出的是0, 1, 2, 3, 4。你可能会问，如果每一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算。

-   **let let for循环**

另外，for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。

```javascript
var len = 5;
for (let i = 0; i < len; i++) {
  let i = 'a';
  setTimeout(() => {
      console.log(i);
  }, 1000 * i);
}
```

**控制台输出**

![]({{ site.url }}/images/posts/bibao4.png)
![]({{ site.url }}/images/posts/bibao5.png)

> 图中可以看出，for()里面的i和{}里的i是同时存在并且处于两个不同的Block之中。

阮一峰老师是这样解释的：上面代码正确运行，输出了 5 次a。这表明函数内部的变量i与循环变量i不在同一个作用域，有各自单独的作用域。

### 这里还总结了一些其他人的理解

下面的回答是依据这个例子来解答的：

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
    // 作用域a
    a[i] = function () {
        // 作用域b
        console.log(i);
    };
}
a[6](); // 10


var a = [];
for (let i = 0; i < 10; i++) {
    a[i] = function () {
        console.log(i);
    };
}
a[6](); // 6
```

-   **One**

原来的使用var的代码，与去糖(desugar)后来看它在执行时是这样的模拟代码:

```javascript
//原来代码
for (var i = 0; i < 10; i++) { setTimeout(()=>console.log("i:",i), 1000); }
```

```javascript
// 不需要加区块符，因为区块也不会影响
var i;
i = 0;
if (i < 10)
    setTimeout(()=>console.log("i:",i), 1000);
    i++;
    if (i < 10)
        setTimeout(()=>console.log("i:",i), 1000);
        i++;
//{{ site.url }}.
```

而使用了let后，会有块级作用域的影响，原来的代码与执行时的去糖模拟代码如下:

```javascript
// 原来代码
for (let i = 0; i < 10; i++) { setTimeout(()=>console.log("i:",i), 1000); }
```

```javascript
// 用区块符区分每次循环的语句
// 每次for语句开始，i指定为一个全域刻度__status，这只是方便说明而已
// __status会记录for语句i最后的值
{
    let i;
    i = 0;
    __status = {i};
}
{ let {i} = __status;
    if (i < 10)
        setTimeout(()=>console.log("i:",i), 1000);
    __status = {i};
}   
    {
        let {i} = __status;
        i++;
        if (i < 10)
            setTimeout(()=>console.log("i:",i), 1000);
        __status = {i};
    }
    //{{ site.url }}.
```

为何可以这样模拟？因为在ES标准中，有一段是关于[CreatePerIterationEnvironment](https://tc39.github.io/ecma262/#sec-createperiterationenvironment)，也就是for语句每次循环所要建立环境的步骤，里面有提及有关词法环境的相关步骤(LexicalEnvironment)，这与使用let时会有关。所以，如果你使用了let而不是var，let的变量除了作用域是在for区块中，而且会为每次循环执行建立新的词法环境(LexicalEnvironment)，拷贝所有的变量名称与值到下个循环执行。以最简单的方式改写原先的问题中的代码，相当于下面这样写:

```javascript
var a = [];
{
    let k;
    for (k = 0; k < 10; k++) {
        let i = k; //注意这里，每次循环都会创建一个新的i变量
        a[i] = function () {
            console.log(i);
        };
    }
}
a[6](); // 6
```

-   **Two**

刚刚想到了一个办法，尝试看看es6经过babel如何转化成es5的。所以大家可以看看转码过后是这样的：

```javascript
"use strict";

var a = [];

var _loop = function _loop(i) {
    a[i] = function () {
        console.log(i);
    };
};

for (var i = 0; i < 10; i++) {
    _loop(i);
}
a[6](); // 6
```

就像用var声明迭代变量的时候，用iife来充当块级作用域一样。但是转到let上，我似乎理解不到迭代变量是如何传递的

```javascript
// 在执行for循环的时候，我能这么理解吗？
{ let i = 0;
    {
        a[i] = function () {
            console.log(i);
        };
        i++;
    }
}
```

-   **Three**

```javascript
for (let i = 0 /* 作用域a */; i < 3; console.log("in for expression", i), i++) {
    let i; //这里没有报错，就意味着这里跟作用域a不同，换做k可能更好理解
    console.log("in for block", i);
}

// 运行结果如下
in for block undefined
in for expression 0
in for block undefined
in for expression 1
in for block undefined
in for expression 2

for (let i = 0; i < 3; console.log("in for expression", i), i++) {
    let k;
    console.log("in for block", k);
}

// 运行结果如下
in for block undefined
in for expression 0
in for block undefined
in for expression 1
in for block undefined
in for expression 2
```

### 参考

-   [维基百科-闭包 (计算机科学)](https://zh.wikipedia.org/wiki/%E9%97%AD%E5%8C%85_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6))
-   [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/let)
-   [怎么理解for循环中用let声明的迭代变量每次是新的变量](https://segmentfault.com/q/1010000007541743)
