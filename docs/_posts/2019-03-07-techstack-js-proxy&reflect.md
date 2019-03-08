---
layout: post
title:  "proxy & reflect"
date:   2019-03-07 20:20
categories: techstack
permalink: /techstack/20190307/proxy
---

**Proxy**
Proxy 对象用于定义基本操作的自定义行为（如属性查找，赋值，枚举，函数调用等）。

**用法**

```javascript
let p = new Proxy(target, handler);
```

Target
	用Proxy包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）。
Handler
	一个对象，其属性是当执行一个操作时定义代理的行为的函数。

* * *

**Reflect**
Reflect 是一个内置的对象，它提供拦截 JavaScript 操作的方法。这些方法与处理器对象的方法相同。Reflect不是一个函数对象，因此它是不可构造的。

你不能将其与一个new运算符一起使用，或者将Reflect对象作为一个函数来调用。Reflect的所有属性和方法都是静态的（就像Math对象）。

* * *

**处理器对象**
处理器对象用来自定义代理对象（target）的各种可代理操作。

|                                           | proxy                        | Object.defineProperty  |
| ----------------------------------------- | ---------------------------- | ---------------------- |
| 监听类型                                      | 可以是任何类型的对象，包括原生数组，函数，甚至另一个代理 | 只能劫持对象的属性，需要深度遍历对象所有属性 |
| 数组的index和length修改、Map、Set、WeakMap、WeakSet | 可以直接监听                       | 无法监听                   |
| 代理对象的操作                                   | 一共有 13 种可代理操作                | 不具备                    |

### demo

-   **VUE 2.0 Array hack**

```javascript
const aryMethods = ['push', 'pop', 'shift', 'unshift', 'splice', 'sort', 'reverse'];
const arrayAugmentations = [];

aryMethods.forEach((method)=> {

    // 这里是原生Array的原型方法
    let original = Array.prototype[method];

   // 将push, pop等封装好的方法定义在对象arrayAugmentations的属性上
   // 注意：是属性而非原型属性
    arrayAugmentations[method] = function () {
        console.log('我被改变啦!');

        // 调用对应的原生方法并返回结果
        return original.apply(this, arguments);
    };

});

let list = ['a', 'b', 'c'];
// 将我们要监听的数组的原型指针指向上面定义的空数组对象
// 别忘了这个空数组的属性上定义了我们封装好的push等方法
list.__proto__ = arrayAugmentations;
list.push('d');  // 我被改变啦！ 4
console.log(list)
// 这里的list2没有被重新定义原型指针，所以就正常输出
// let list2 = ['a', 'b', 'c'];
// list2.push('d');  // 4
```

-   **Proxy Array**

```javascript
// 初始数组
// const arr = {aa: 1};
const arr = [1, 2, 3, 4];
// 监听数组
const newArr = new Proxy(arr, {
  get: function(target, key, receiver) {
    console.log(target, key, receiver);
    return Reflect.get(target, key, receiver);
  },
  set: function(target, key, value, receiver) {
    console.log(target, key, value, receiver);
//     if (key !== 'length') {
//       console.log(value);
//     }
    return Reflect.set(target, key, value, receiver);
  }
});

// newArr.length = 10;
// newArr.aa = 2;
newArr.push(6);
// newArr.values;
```

![]({{ site.url }}/images/posts/proxy1.png)

**为什么Array.push()会执行两次get和两次set操作？**

**Definition of Array.push**：The push() method adds one or more elements to the end of an array and returns the new length of the array.
> 所以，前两次log是get函数打印的，分别是获取当前要被操作的数组，和当前数组的长度（长度是用来确认即将插入的数据的位置）；第三条log是set函数打印的，分别表示在数组index为4的地方插入的值是6，第四条log是数组在插入值之后，设置数组最新的length值并返回

### Page example

-   [**Object.defineProperty双向数据绑定实例**]({{ site.url }}/demo/proxy/defineProperty.html)

-   [**Proxy双向数据绑定实例**]({{ site.url }}/demo/proxy/Proxy.html)


### 参考

-   [实现双向绑定Proxy比defineproperty优劣如何?](https://juejin.im/post/5acd0c8a6fb9a028da7cdfaf)
-   [Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
-   [Reflect](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)
-   [处理器对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler)
