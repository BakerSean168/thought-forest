---
tags:
  - tech/dev/frame
  - type/concept
  - status/growing
description: jsx
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[React MOC]] | [[前端基础 MOC]]

---


# JSX 全面指南

## 基础概念

### 什么是JSX

- JSX (JavaScript XML) 是JavaScript的语法扩展
- 允许在JavaScript代码中编写类似HTML的标记
- 不是字符串也不是HTML，最终会被编译为React.createElement()调用

### JSX的特点

- 类HTML语法，更直观地描述UI
- 完全采用JavaScript的表达能力
- 编译时检查，更早发现错误
- 防止XSS(跨站脚本攻击)默认转义

### JSX与HTML的区别

1. 属性命名采用camelCase (className代替class)
2. 必须闭合标签 (<input />)
3. 样式属性接收对象而非字符串
4. 事件处理使用函数而非字符串

## 使用指南

### 基本语法

```jsx
const element = <h1>Hello, world!</h1>;
```

### JSX 规则

```js
<h1>海蒂·拉玛的待办事项</h1>
<img
  src="https://i.imgur.com/yXOvdOSs.jpg"
  alt="Hedy Lamarr"
  class="photo"
>
<ul>
  ...
</ul>
```

#### 1. 只能返回一个根元素

例子：
  上方代码外侧必须使用`<div></div`或`<></>`标签包裹  

原因：  
 JSX 虽然看起来很像 HTML，但在底层其实被转化为了 JavaScript 对象，你不能在一个函数中返回多个对象，除非用一个数组把他们包装起来。

#### 2. 标签必须闭合

`<img ...>` `<img .../>`

#### 3. 使用驼峰式命名法给大部分属性命名！

JSX 最终会被转化为 JavaScript，而 JSX 中的属性也会变成 JavaScript 对象中的键值对。  
在你自己的组件中，经常会遇到需要用变量的方式读取这些属性的时候。但 JavaScript 对变量的命名有限制。  
  例如，变量名称不能包含 - 符号或者像 class 这样的保留字。
这就是为什么在 React 中，大部分 HTML 和 SVG 属性都用驼峰式命名法表示。  
  例如，需要用 strokeWidth 代替 stroke-width。由于 class 是一个保留字，所以在 React 中需要用 className 来代替。
```js
<img 
  src="https://i.imgur.com/yXOvdOSs.jpg" 
  alt="Hedy Lamarr" 
  className="photo"
/>
```

**建议使用 [转化器](https://transform.tools/html-to-jsx) 将 HTML 和 SVG 标签转化为 JSX。**
### 嵌入表达式

```jsx
const name = 'John';
const element = <h1>Hello, {name}</h1>;
```

### 属性设置

```jsx
const element = <img src={user.avatarUrl} alt="User" />;
```

### 子元素

```jsx
const element = (
<div>
<h1>Hello!</h1>
<h2>Good to see you here.</h2>
</div>
);
```

### 条件渲染

```jsx
{isLoggedIn ? <LogoutButton /> : <LoginButton />}
```

### 列表渲染

```jsx
<ul>
{numbers.map((number) =>
<li key={number.toString()}>{number}</li>
)}
</ul>
```

## 实战经验

### 最佳实践

1. 组件命名使用PascalCase
2. 每个文件只导出一个主要组件
3. 复杂逻辑提取为独立函数
4. 保持JSX简洁，避免嵌套过深

### 常见模式

```jsx
// 渲染属性模式
<DataProvider render={data => (
<h1>Hello {data.target}</h1>
)}/>

// 子组件函数
<Mouse>
{mouse => (
<p>鼠标位置: {mouse.x}, {mouse.y}</p>
)}
</Mouse>
```

### 性能优化

1. 避免在渲染方法中创建新对象/函数
2. 使用key属性优化列表渲染
3. 合理使用React.memo
4. 复杂条件逻辑使用提前返回

### 常见错误

1. 忘记闭合标签
2. 错误使用内联样式对象
3. 混淆JSX与HTML属性名
4. 忘记给列表项添加key

## 经验总结

### 开发心得

1. JSX使UI代码更直观易读
2. 组合优于继承的设计理念
3. 单向数据流简化状态管理
4. 声明式编程提升开发效率

### 调试技巧

1. 使用React Developer Tools
2. 检查JSX编译后的代码
3. 添加临时console.log
4. 验证propTypes

### 学习路径

1. 先掌握JSX基础语法
2. 理解虚拟DOM概念
3. 练习组件组合
4. 学习高级模式(Render Props/HOC)

## 信息参考

### 官方文档

- [React官方JSX介绍](https://reactjs.org/docs/introducing-jsx.html)
- [JSX深度指南](https://reactjs.org/docs/jsx-in-depth.html)

### 推荐资源

1. 《React设计模式与最佳实践》
2. EpicReact.dev JSX课程
3. Frontend Masters的React课程
4. React官方教程和文档

### 工具推荐

1. Babel JSX转换器
2. ESLint with React插件
3. Prettier代码格式化
4. CodeSandbox在线编辑器
