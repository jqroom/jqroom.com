---
layout: post
title:  "JavaScript中的this"
date:   2018-06-01 17:30
categories: techstack
permalink: /techstack/20180601/this-in-javascript
---

this常见的指向以及this在箭头函数中的表现

## this常见的指向

-   1.函数调用模式的时候，this指向window

```javascript
function aa(){
  console.log(this)
}
aa()                 //window
```

-   2.方法调用模式的时候，this指向方法所在的对象

```javascript
var a={};
a.name = 'hello';
a.getName = function(){
  console.log(this.name)
}
a.getName()         //'hello'
```

-   3.构造函数模式的时候，this指向新生成的实例

```javascript
function Aaa(name){
  this.name= name;
  this.getName=function(){
    console.log(this.name)
  }
}
var a = new Aaa('kitty');
a.getName()        //  'kitty'
var b = new Aaa('bobo');
b.getName()        //  'bobo'
```

-   4.apply/call调用模式的时候，this指向apply/call方法中的第一个参数

```javascript
var list1 = {name:'andy'}
var list2 = {name:'peter'}

function d(){
  console.log(this.name)
}
d.call(list1)     //  'andy'
d.call(list2)     //  'peter'
```

## this在箭头函数中的表现

```javascript
window.dam = 'w';
var a = {
    init() {
        this.bar = () => this.dam
    },
    dam: 'a',
    foo() {
        return this.dam
    }
}

var b = {
    dam: 'b'
}

a.init()

console.log(a.foo.bind(b)()); // b
console.log(a.bar.bind(b)()); // a
```

> 箭头函数里面的this是不能被call、apply或bind所改变的。(If Target is an arrow function or a bound function then the thisArg passed to this method will not be used by subsequent calls to F.)

## 接着深入分析一下bind方法

> bind() 函数会创建一个新函数（称为绑定函数），新函数与被调函数（绑定函数的目标函数）具有相同的函数体（在 ECMAScript 5 规范中内置的call属性）。**当新函数被调用时 this 值绑定到 bind() 的第一个参数，该参数不能被重写**(所以多次调用bind方法的时候，只是第一次bind有效)。绑定函数被调用时，bind() 也接受预设的参数提供给原函数。**一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数**。

```javascript
// MDN polyfill
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {
    if (typeof this !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }

    var aArgs   = Array.prototype.slice.call(arguments, 1),
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
          return fToBind.apply(this instanceof fNOP
                 ? this
                 : oThis,
                 // 获取调用时(fBound)的传参.bind 返回的函数入参往往是这么传递的
                 aArgs.concat(Array.prototype.slice.call(arguments)));
        };

    // 维护原型关系
    if (this.prototype) {
      // Function.prototype doesn't have a prototype property
      fNOP.prototype = this.prototype;
    }
    fBound.prototype = new fNOP();

    return fBound;
  };
}
```

和例子一起分析

```javascript
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function() {
  return this.x + ',' + this.y;
};

var p = new Point(1, 2);
p.toString(); // '1,2'

var YAxisPoint = Point.bind(null, 0/*x*/);

var axisPoint = new YAxisPoint(5/*y*/);
console.log(axisPoint.toString()); // '0,5'  new Function的时候，bind中的this被忽略

axisPoint instanceof Point; // true
axisPoint instanceof YAxisPoint; // true
new Point(17, 42) instanceof YAxisPoint; // true
```

## 参考

-   [JavaScript中的this](https://segmentfault.com/a/1190000000638443)
-   [javascript关于bind方法的实现](https://segmentfault.com/q/1010000002874323)
-   [Function.prototype.bind()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [Ecma-262](https://www.ecma-international.org/publications/standards/Ecma-262.htm)
