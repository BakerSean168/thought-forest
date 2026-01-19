---
tags:
  - tech/dev/frontend
  - type/concept
  - status/seed
description: react-intl
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[ECMAScript MOC]]

---


# react-intl 

## 基础概念

在 `react-intl` 库中，`intl` 是一个核心对象，它封装了国际化相关的所有功能，用于处理多语言文本翻译、日期/时间格式化、数字格式化、复数处理等国际化场景。你提供的代码中，`intl` 通常是通过 `react-intl` 的上下文（Context）传递到组件中的，用于在组件内获取翻译文本或执行格式化操作。


### 一、`intl` 对象的来源
在 `react-intl` 中，`intl` 对象由顶层的 `<IntlProvider>` 组件创建并注入到 React 上下文（Context）中。所有被 `<IntlProvider>` 包裹的子组件，都可以通过以下方式获取 `intl` 对象：

1. **通过 `injectIntl` 高阶组件（HOC）注入**（你的代码可能使用了这种方式）：  
   组件通过 `injectIntl` 包装后，`intl` 会作为 props 传入组件，就像你代码中 `props.intl` 这样。

   示例：
   ```jsx
   import { injectIntl } from 'react-intl';

   const ReactionsButton = (props) => {
     const { intl } = props; // 从 props 中获取 intl
     // ...
   };

   export default injectIntl(ReactionsButton); // 通过高阶组件注入
   ```

2. **通过 `useIntl` 钩子获取**（React 函数组件推荐方式）：  
   在函数组件中，可以直接使用 `useIntl` 钩子获取 `intl` 对象，无需通过 props 传递。

   示例：
   ```jsx
   import { useIntl } from 'react-intl';

   const ReactionsButton = () => {
     const intl = useIntl(); // 直接通过钩子获取
     // ...
   };
   ```


### 二、`intl` 对象的核心方法
`intl` 对象提供了一系列方法，最常用的是处理文本翻译的 `formatMessage`，以及格式化日期、数字等的方法。


#### 1. `formatMessage`：获取翻译文本（最常用）
用于根据国际化配置中的「消息键（message key）」获取对应的翻译文本，配合 `defineMessages` 定义的消息对象使用。

示例：
```jsx
// 1. 先用 defineMessages 定义消息（通常放在组件顶部或单独的国际化文件中）
import { defineMessages } from 'react-intl';

const messages = defineMessages({
  like: {
    id: 'reactions.like', // 唯一消息键（对应国际化文件中的 key）
    defaultMessage: 'Like', // 默认文本（当没有对应语言翻译时使用）
    description: 'Text for the like reaction button' // 描述（方便翻译人员理解上下文）
  },
  love: {
    id: 'reactions.love',
    defaultMessage: 'Love',
    description: 'Text for the love reaction button'
  }
});

// 2. 在组件中用 intl.formatMessage 获取翻译
const ReactionsButton = (props) => {
  const { intl } = props;
  return (
    <div>
      <button>{intl.formatMessage(messages.like)}</button> // 渲染翻译后的"点赞"（假设中文配置）
      <button>{intl.formatMessage(messages.love)}</button> // 渲染翻译后的"爱心"
    </div>
  );
};
```

如果国际化文件（如中文）中配置了：
```json
{
  "reactions.like": "点赞",
  "reactions.love": "爱心"
}
```
则 `intl.formatMessage(messages.like)` 会返回 ` "点赞"`。


#### 2. 其他常用方法
- **`formatDate`**：格式化日期  
  ```jsx
  intl.formatDate(new Date(), { year: 'numeric', month: 'long', day: 'numeric' });
  // 中文环境可能返回："2025年10月23日"
  ```

- **`formatNumber`**：格式化数字（货币、百分比等）  
  ```jsx
  intl.formatNumber(1000, { style: 'currency', currency: 'CNY' }); // 中文环境返回："¥1,000.00"
  ```

- **`formatPlural`**：处理复数（不同语言复数规则不同）  
  ```jsx
  intl.formatPlural(5, { one: 'comment', other: 'comments' }); // 英文环境返回："comments"
  ```


### 三、`intl` 对象的作用
`intl` 是 `react-intl` 实现国际化的核心载体，它的作用可以概括为：  
1. 统一管理当前语言环境（如中文、英文）的翻译文本，通过 `formatMessage` 按 key 取出对应语言的文本。  
2. 处理不同语言的格式化规则（日期、时间、数字、复数等），确保应用在不同语言环境下展示符合当地习惯的格式。  


### 总结
你代码中的 `intl` 是 `react-intl` 提供的国际化工具对象，通过它可以方便地在组件中获取翻译文本、格式化日期/数字等。最常用的是 `intl.formatMessage` 方法，配合 `defineMessages` 定义的消息对象，实现多语言文本的动态切换。

一个 react 下的国际化库


## 使用指南

## 实战经验

## 经验总结

## 信息参考

