---
layout: post
title:  "常见算法"
date:   2021-03-30 08:38
categories: algorithm
permalink: /algorithm/20210330
---

### 求一个数字数组中连续的数字和的最大值
```javascript
function getMaxNum2(arr){
    let tmp = 0
    let max = 0

    for(let i = 0; i < arr.length; i++){
        if(arr[i] > 0){
            tmp += arr[i]
            max = tmp
        }else if(tmp + arr[i] < 0){
            tmp = 0
        }
    }
    console.log(max)
}
getMaxNum2([1, 2, 3, -2, -1, 4])
```

### 爬楼梯问题，每次可以爬一格，也可以选择爬两格，求到第N格有多少种走法
```javascript
function step(n){
    if(n <= 2) return n

    let arr = [0, 1, 2]
    for(let i = 3; i <= n; i++){
        arr[i] = arr[i - 1] + arr[i - 2]
    }
    console.log(arr[n])
}

step(6)
```
