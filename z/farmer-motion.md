---
tags:
  - status/growing
  - type/resource
  - tech/dev
  - tech/framework/react
  - tech/dev/library
description: "**Framer Motion** 是目前 React 生态中最流行、功能最强大且易于使用的动画库之一。它由设计工具公司 Framer 开发，旨在弥合设计与开发之间的差距，让开发者能够以极其简单的**声明式（Declarative）**语法实现复杂的动画效果。"
created: 2026-01-13T20:15:25
updated: 2026-01-13T20:15:44
---

# farmer-motion 

### 1. 核心特点

- **声明式 API**：你不需要手写复杂的过渡逻辑，只需定义“初始状态”和“结束状态”，库会自动处理中间的过渡。
- **物理弹簧 (Spring Physics)**：这是它的杀手锏。默认动画使用物理模拟（模拟弹簧的刚度、阻尼），而不是生硬的时间曲线（如 ease-in-out），让交互感觉非常自然和流畅。
- **强大的手势系统**：内置了 `hover`（悬停）、`tap`（点击/轻按）、`drag`（拖拽）和 `pan`（平移）等手势监听。
- **布局动画 (Layout Animations)**：只需添加一个 `layout` 属性，当 DOM 结构发生变化（如列表重新排序）时，元素会自动平滑地移动到新位置。
- **AnimatePresence**：解决了 React 中最大的痛点之一——**组件卸载时的离场动画**。

---

### 2. 快速入门

#### 安装

```
npm install framer-motion
# 或者
yarn add framer-motion
```

#### 基本用法 (`<motion.div>`)

Framer Motion 的核心是 `motion` 组件。它对标准的 HTML 标签（如 `div`, `span`, `h1`）进行了增强。

JavaScript

```
import { motion } from "framer-motion";

export const MyComponent = () => (
  <motion.div
    // 1. 初始状态
    initial={{ opacity: 0, x: -100 }}
    // 2. 动画目标状态
    animate={{ opacity: 1, x: 0 }}
    // 3. 过渡配置 (可选)
    transition={{ duration: 0.5 }}
  >
    Hello Motion
  </motion.div>
);
```

**解释：** 组件挂载时，会从左侧 100px 且透明的位置，平滑移动到原位并完全不透明。

---

### 3. 进阶核心概念

#### A. Variants (变量/编排)

当动画变得复杂，或者需要**父子组件动画同步**（例如：父组件出现后，子组件依次进场）时，使用 `variants` 是最佳实践。

JavaScript

```
const listVariants = {
  hidden: { opacity: 0 },
  visible: { 
    opacity: 1, 
    transition: { staggerChildren: 0.2 } // 关键：控制子元素依次延迟显示
  }
};

const itemVariants = {
  hidden: { x: -20, opacity: 0 },
  visible: { x: 0, opacity: 1 }
};

return (
  <motion.ul
    initial="hidden"
    animate="visible"
    variants={listVariants}
  >
    <motion.li variants={itemVariants}>项目 1</motion.li>
    <motion.li variants={itemVariants}>项目 2</motion.li>
    <motion.li variants={itemVariants}>项目 3</motion.li>
  </motion.ul>
);
```

#### B. 手势交互 (Gestures)

无需添加复杂的 Event Listener，直接使用 props。

JavaScript

```
<motion.button
  whileHover={{ scale: 1.1 }} // 悬停时放大
  whileTap={{ scale: 0.9 }}   // 点击时缩小
  drag                        // 开启拖拽
  dragConstraints={{ left: 0, right: 300 }} // 限制拖拽范围
>
  拖我
</motion.button>
```

#### C. 离场动画 (AnimatePresence)

在 React 中，当组件从 DOM 中移除时，通常会立即消失。用 `AnimatePresence` 包裹可以实现“先做完动画再移除”。

JavaScript

```
import { motion, AnimatePresence } from "framer-motion";

export const Slideshow = ({ isVisible }) => (
  <AnimatePresence>
    {isVisible && (
      <motion.div
        key="modal" // 必须有唯一的 key
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        exit={{ opacity: 0 }} // 组件卸载时执行的动画
      >
        我是弹窗内容
      </motion.div>
    )}
  </AnimatePresence>
);
```

---

### 4. 为什么选择 Framer Motion？

|**特性**|**描述**|
|---|---|
|**开发体验 (DX)**|API 设计非常符合 React 的思维模式，代码可读性极高。|
|**性能**|使用硬件加速，底层优化良好。对于极高频的动画，还提供了 `useTransform` 和 `useMotionValue` 来绕过 React 渲染循环。|
|**社区支持**|文档非常完善，社区有大量的示例和教程。|
|**体积**|稍微偏大（相比专门的微型库），但支持 Tree-shaking（按需加载），可以通过 `LazyMotion` 进一步优化体积。|

### 5. 总结

如果你正在开发 React 应用（尤其是使用 Next.js），并且需要**稍微复杂一点的交互**（比如侧边栏滑出、模态框弹出、列表排序、路由切换动画），**Framer Motion 是目前的首选方案**。它虽然比纯 CSS 动画稍重，但带来的开发效率提升和交互质感是巨大的。

**你现在想尝试写一个具体的动画效果吗？** 比如一个“点击展开的卡片”或者“页面切换过渡”？我可以为你提供具体的代码示例。