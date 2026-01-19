---
tags:
  - tech/lang/algorithm
  - type/snippet
  - status/growing
description: 百度笔试-好数组问题解法及ceil/floor函数区别
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Algorithm MOC]] | [[面试题汇总]]

---


# 百度-好数组-ceil-floor的区别 

## 题目

我刚刚在笔试的时候遇到了一个问题，算法题的题目是：
当数组中所有数据的最大值不超过最小值的2倍时，就称它是一个好数组。
然后可以做一个操作，就是把一个数拆分成两个数，比如说一个数字五可以把它拆分成2加3。然后可以进行这种操作，然后给你一个数组，问你他需要进行最少几次操作，变成一个好数组。  

## 题解

我想的话这个方法就是先变立数组，找到它的最小值，然后再算出目标值，就是最小值乘2。然后在遍历数组中每个值。然后当他大于这个数字时，就计算他除以这个目标值的floor，即小于他的最大整数。我觉得这个应该就是最小的。但是测试用例时我却错了，你觉得这个应该是怎么做的？

方向对了，但是细节不对

```javascript


function b(arr) {
    const minNum = Math.min(...arr)
    const range = 2 * minNum
    let ans = 0;
    for (const n of arr) {
        if (n > range) {

            ans += checkTimes(range, n);
        }
    }
    console.log(ans)
}

function checkTimes(target, parm) {
    let times = Math.floor(parm/target);
    console.log(times)
    
    return times;
}

let test1 = [3, 6, 13]

// b(test1)
checkTimes(6,13)

```

真正的操作次数应该是“段数减 1”：`Math.ceil(n / target) - 1` 
`Math.floor(parm / target)` 还是不行：它算的是“至少需要多少个完整的 `target` 容量”而不是要把这个数拆成多少段。

