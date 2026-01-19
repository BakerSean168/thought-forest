---
tags:
  - tech/lang/nodejs
  - type/snippet
  - status/evergreen
description: ACM模式算法的输入输出（nodejs-js）
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Node.js MOC]] | [[后端框架 MOC]]

---


## 基础的读取

nodejs 环境下通过 readline 模块读取数据  

line 数据特征：  
去除了最后的空格符

### 每行简单读取

```js
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.on('line', (line) => {
  const [a, b] = line.trim().split(' ').map(Number);
  console.log(a + b);
});
```

### 多组数据读取

一般会第一行为组数，后面为每组数据，此时需要引入 N（当前组要处理的组数）、count（已处理数据组数）

```js
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

let N = 0;
let count = 0;

rl.on('line', (line) => {
  if (N === 0) {
    // 读取每组数据的第一个数字N
    N = parseInt(line.trim(), 10);
    count = 0;
  } else {
    // 处理每对a和b
    const [a, b] = line.trim().split(' ').map(Number);
    console.log(a + b);
    count++;
    
    // 当处理完N对数据后，重置N为0以准备读取下一组
    if (count === N) {
      N = 0;
    }
  }
});
```

