---
title: js中的浮点数计算
categories:
  - coding
  - js
tags:
  - 浮点数
date: 2018-05-18 20:27:09
update: 2018-05-18 20:27:09
---

由于`舍入误差`的存在，浮点数的计算会不是很精确。例如`1.11 * 100 => 111.00000000000001`。

下面是一段简单处理的代码：
```
//按照有效数字位数进行四舍五入，默认6位有效数字
Math.signFigures = function(num, rank = 6) {
    if(!num) return(0);
    let sign = num / Math.abs(num);
    let number = num * sign;
    let temp = rank - 1 - Math.floor(Math.log10(number));
    let ans;
    if (temp > 0) {
        ans = parseFloat(number.toFixed(temp));
    } else if (temp < 0) {
        temp = Math.pow(10, temp);
        ans = Math.round(number / temp) * temp;
    } else {
        ans = Math.round(number);
    }
    return (ans * sign);
};
```

Math.signFigures(123.242545, 1) 会出问题！

## 参考
1. [为什么js里面有些数字*100结果会不准确?](https://segmentfault.com/q/1010000005697295)