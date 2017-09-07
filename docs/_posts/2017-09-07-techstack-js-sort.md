---
layout: post
title: "Array.prototype.sort()"
date: '2017-09-07 21:46'
categories: techstack
permalink: /techstack/js/20170907/sort
---

### 常见面试题，使用对一个对象数组进行排序。

```javascript
var list = [{name: 'dafhs', age: 10}, {name: 'Adou', age: 18}, {name: 'caiDan', age: 15}, {name: 'box', age: 16}];
```
###### 1. 对list按照age从小到大排序
- 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
- 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。
- 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前

```javascript
list.sort((x, y) => {
    return x.age - y.age;
});
```
输出：`[{name: "dafhs", age: 10},{name: "caiDan", age: 15},{name: "box", age: 16},{name: "Adou", age: 18}]`

###### 2. 对list按照name的首字母顺序升序排序(方法1)
- 取出每个单词的首字母进行比较

```javascript
list.sort((x, y) => {
    let n1 = x.name.charAt(0).toLowerCase();
    let n2 = y.name.charAt(0).toLowerCase();
    if(n1 > n2){
        return 1;
    }

    if(n1 < n2){
        return -1;
    }

    return 0;
});
```
输出：`[{name: "Adou", age: 18},{name: "box", age: 16},{name: "caiDan", age: 15},{name: "dafhs", age: 10}]`

###### 3. 对list按照name的首字母顺序升序排序(方法2)
- 当n1 > n2时，+(n1 > n2)的值为1，返回1；
- 当n1 == n2时，+(n1 > n2)的值为0，+(n1 === n2) - 1的值也为0，返回0；
- 当n1 < n2时，+(n1 > n2)的值为0，+(n1 === n2) - 1的值也为-1，返回-1；
- +(n1 === n2) - 1也可以改成+(n1 >= n2) - 1，主要就是为了当n1 < n2的时候，+( XX ) == 0

```javascript
list.sort((x, y) => {
    let n1 = x.name.charAt(0).toLowerCase();
    let n2 = y.name.charAt(0).toLowerCase();
    return +(n1 > n2) || +(n1 === n2) - 1;
});
```
输出：
`[{name: "Adou", age: 18},{name: "box", age: 16},{name: "caiDan", age: 15},{name: "dafhs", age: 10}]`

### 参考

- [Array.prototype.sort() en-US](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
- [Array.prototype.sort() en-CN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
