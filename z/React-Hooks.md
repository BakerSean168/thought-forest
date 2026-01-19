---
tags:
  - tech/dev/frame
  - type/concept
  - status/growing
description: React Hooks 钩子函数使用指南
created: 2025-01-01T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[React MOC]] | [[React中的钩子]]

---


# React-Hooks 

## 基础概念

React 中允许不编写类即可实现状态和其他 React 功能的函数。

## 使用指南

### `useState(initialState)`

Call `useState` at the top level of your component to declare a [state variable.](https://react.dev/learn/state-a-components-memory)

```tsx
import { useState } from 'react';

function MyComponent() {
  const [age, setAge] = useState(28);
  const [name, setName] = useState('Taylor');
  const [todos, setTodos] = useState(() => createTodos());
  // ...
```

The convention is to name state variables like `[something, setSomething]` using [array destructuring.](https://javascript.info/destructuring-assignment)

`useState` 返回一个包含恰好两个值的数组：
1. 当前的状态值。在首次渲染期间，它将与你传递的 `initialState` 相匹配。
2. `set` 函数，允许您将状态更新为不同的值并触发重新渲染。

- 如果你传递一个函数作为 `initialState` ，它将被视为一个初始化函数。它应该是纯函数，不应接受任何参数，并返回任意类型的值。React 将在初始化组件时调用你的初始化函数，并将其返回值存储为初始状态。

> [!NOTE] 注意事项
> - set 函数只会在下一次渲染时更新变量。在 set 后立刻读取 state，仍会得到旧的 state。
> - React 会批量处理状态更新。它会在所有事件处理程序运行并调用它们的 `set` 函数后更新屏幕。这可以防止在单个事件期间发生多次重新渲染。在极少数情况下，如果您需要强制 React 更早更新屏幕，例如访问 DOM，可以使用 `flushSync` 。
> - 如果你提供的新值与当前的 `state` 相同（通过 `Object.is` 比较确定），React 将会跳过重新渲染该组件及其子组件。这是一种优化。尽管在某些情况下，React 在跳过子组件之前可能仍需要调用你的组件，但这不会影响你的代码。
> - `set` 函数具有稳定的身份，因此您经常会看到它被省略在 Effect 依赖项中，但包含它不会导致 Effect 触发。如果 linter 允许您省略依赖项而不报错，这样做是安全的。
> - 在渲染期间调用 `set` 函数只允许在当前正在渲染的组件内部进行。React 将丢弃其输出并立即尝试使用新状态重新渲染它。这种模式很少需要，但你可以用它来存储先前渲染中的信息。
> - 在严格模式下，React 会调用你的更新函数两次，以帮助你发现意外的副作用。这是仅在开发环境中的行为，不会影响生产环境。如果你的更新函数是纯函数（应该是纯函数），这不会影响行为。其中一次调用的结果将被忽略。

### useEffect(setup, dependencies?)

在组件的顶层调用 useEffect 来声明一个副作用（Effect）：

```jsx
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [serverUrl, roomId]);
  // ...
}
```

查看下方更多示例。

参数
- `setup`：包含副作用逻辑的函数。该函数还可以选择性地返回一个清理函数。当组件被添加到 DOM 时，React 会执行这个 setup 函数。在每次依赖项变化导致的重新渲染后，React 会先执行清理函数（如果提供了的话，使用旧值），然后再执行带有新值的 setup 函数。当组件从 DOM 中移除时，React 会执行清理函数。
- 可选的 `dependencies`：setup 代码中引用的所有响应式值的列表。响应式值包括 props、state，以及所有在组件体内直接声明的变量和函数。如果你的代码检查工具（linter）配置了 React 规则，它会验证每个响应式值是否都被正确指定为依赖项。依赖列表的项数必须是固定的，并且要像 `[dep1, dep2, dep3]` 这样内联编写。React 会使用 `Object.is` 比较每个依赖项与其先前的值。如果你省略这个参数，副作用会在组件每次重新渲染后都重新执行。查看传递依赖数组、空数组和不传递依赖项这三种情况的区别。

返回值
useEffect 返回 undefined。

**注意事项**
- useEffect 是一个 Hook，因此你只能在组件的顶层或自定义 Hook 中调用它。不能在循环或条件语句中调用。如果需要这样做，可以提取一个新组件并将状态移到其中。
- 如果你不是要与某些外部系统同步，可能根本不需要使用 Effect。
- 当严格模式（Strict Mode）开启时，React 会在第一次真正执行 setup 之前，额外运行一次仅用于开发环境的 setup+cleanup 循环。这是一种压力测试，确保你的清理逻辑与 setup 逻辑“对称”，并且能停止或撤销 setup 所做的一切。如果这导致了问题，请完善清理函数。
- 如果某些依赖项是在组件内部定义的对象或函数，可能会导致 Effect 比必要的更频繁地重新执行。要解决这个问题，可以移除不必要的对象和函数依赖项。你也可以将状态更新和非响应式逻辑提取到 Effect 外部。
- 如果你的 Effect 不是由交互（如点击）触发的，React 通常会先让浏览器绘制更新后的界面，然后再运行你的 Effect。如果你的 Effect 正在执行一些视觉相关的操作（例如定位工具提示），且延迟很明显（例如出现闪烁），可以用 useLayoutEffect 替代 useEffect。
- 如果你的 Effect 是由交互（如点击）触发的，React 可能会在浏览器绘制更新后的界面之前运行你的 Effect。这确保了 Effect 的结果能被事件系统捕获。通常这会按预期工作。不过，如果你必须将工作推迟到绘制之后（例如显示 alert()），可以使用 setTimeout。详见 reactwg/react-18/128。
- 即使你的 Effect 是由交互（如点击）触发的，React 也可能允许浏览器在处理 Effect 内部的状态更新之前重新绘制界面。通常这会按预期工作。但如果你必须阻止浏览器重新绘制，可以用 useLayoutEffect 替代 useEffect。
- Effects 只在客户端运行，不会在服务器渲染期间执行。

### `useRef(initialValue)`

`useRef` 是一个 React Hook，可以让你引用一个不需要用于渲染的值。

```tsx
import { useRef } from 'react';

function MyComponent() {
  const intervalRef = useRef(0);
  const inputRef = useRef(null);
  // ...
```

参数：
initialValue : 你希望 ref 对象的 current 属性初始具有的值。它可以是任何类型的值。此参数在初始渲染后被忽略。

useRef 返回一个具有单个属性的对象：
- current : 初始时，它被设置为传递的 initialValue 。之后您可以将其设置为其他内容。如果您将 ref 对象作为 ref 属性传递给 JSX 节点，React 将设置其 current 属性。

在后续的渲染中， useRef 将返回相同的对象。
## 信息参考
