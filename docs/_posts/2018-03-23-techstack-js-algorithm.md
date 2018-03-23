---
layout: post
title:  "用javascript来写几个常见的算法"
date:   2018-03-23 17:48
categories: techstack
permalink: /techstack/20180323/algorithm
---

冒泡排序、快速排序、插入排序；fabonacci；深度拷贝、深度继承。

## 排序算法

-   ### 冒泡排序

```javascript
function upSort(arr){
    for(var i = 0; i < arr.length; i++){
        for(var j = i + 1; j < arr.length; j++){
            if(arr[i] > arr[j]){
                var temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
    }
    return arr;
}

// test
var arrTest = [85, 24, 63, 45, 17, 31, 96, 50];
upSort(arrTest);
```

-   ### 快速排序

```javascript
function quickSort(arr){
    var len = arr && arr.length;
    if(len <= 1){
        return arr;
    }

    var index = Math.floor(len / 2);
    var mVal = arr.splice(index, 1)[0];
    var left = [];
    var right = [];
    for(var i = 0; i < arr.length; i++){
        if(arr[i] < mVal){
            left.push(arr[i]);
        }else{
            right.push(arr[i]);
        }
    }

    return quickSort(left).concat([mVal], quickSort(right));
}

// test
var arrTest = [85, 24, 63, 45, 17, 31, 96, 50];
quickSort(arrTest);
```

-   ### 插入排序

```javascript
function insertSort(arr){
    var newArr = [];
    for(var i = 0; i < arr.length; i++){
        var j = i - 1;
        while(j >= 0 && newArr[j] > arr[i]){
            newArr[j+1] = newArr[j];
            j--;
        }
        newArr[j+1] = arr[i];
    }
    return newArr;
}

// test
var arrTest = [85, 24, 63, 45, 17, 31, 96, 50];
insertSort(arrTest);
```

## 递归算法与优化

-   ### fabonacci

```javascript
var cache = {};
function fabonacciFun(n) {
    if (n == 0) {
        return 0;
    }

    if (n == 1) {
        return 1;
    }

    if (typeof cache[n] == 'number') {
        return cache[n];
    } else {
        var result = cache[n] = fabonacciFun(n - 1) + fabonacciFun(n - 2)
        return result;
    }
}
console.time();
fabonacciFun(10)
console.timeEnd();
```

## 对象继承与拷贝

-   ### 深度拷贝

```javascript
function getType(obj){
    return Object.prototype.toString.call(obj).slice(8, -1).toLowerCase();
}
function deepCopy(obj){
    var objType = getType(obj);
    if(objType === 'null' || objType === 'undefined'){
        return obj;
    }

    var newObj;
    if(objType == 'array'){
        newObj = [];
    }else{
        newObj = {};
    }

    for(var i in obj){
        if(obj.hasOwnProperty(i)){
            if(getType(obj[i]) === 'object' || getType(obj[i]) === 'array'){
                newObj[i] = deepCopy(obj[i]);
            }else{
                newObj[i] = obj[i];
            }

        }
    }
    return newObj;
}

// test
// var dataTest =  [1, {a: 2}, 3, {d: 4}, [5]];
var dataTest = {a:[1,2], b: function(){}};
var bbTest = deepCopy(dataTest);
dataTest.b = 1;
console.log(dataTest);
console.log(bbTest);
```

-   ### 深度继承

```javascript
function deepExtend(out) {
    out = out || {};

    for (var i = 1; i < arguments.length; i++) {
        var obj = arguments[i];

        if (!obj)
            continue;

        for (var key in obj) {
            if (obj.hasOwnProperty(key)) {
                if (typeof obj[key] === 'object' && obj[key] !== null)
                    out[key] = this.deepExtend(out[key], obj[key]);
                else
                    out[key] = obj[key];
            }
        }
    }

    return out;
}
```
