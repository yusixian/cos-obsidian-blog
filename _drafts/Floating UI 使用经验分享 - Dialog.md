---
title: Floating UI 使用经验分享 - Dialog
link: floating-ui-experience-dialog
catalog: true
date: 2023-05-02 17:17:12
tags:
- 前端
- floating-ui
categories:
- [笔记, 前端]
---
%% #前端 #floating-ui  %%

上文：[[Floating UI 使用经验分享 - Popover]]

> 写这篇文章我更愿意称之为在React中使用 Floating UI 的经验分享，特别是**在 React 中使用该库**的方法，而不是教程，因为实际运用的时候发现Floating UI 的文档和示例已经相当详尽，但苦于Floating UI的中文资料寥寥无几，所以自己沉淀一些方便日后回忆，也希望能为需要的人提供一些帮助和参考。（当然，最快的还是直接去看英文文档和例子，搜索 API 的使用）


大部分内容翻译自 [Dialog | Floating UI](https://floating-ui.com/docs/dialog) 外加自己的一些润色。

在本文中，我将分享如何使用 Floating UI 来创建另一种常见的浮动 UI 组件——**Dialog（对话框）**。Dialog 是一个浮动元素，显示需要立即关注的信息，他会出现在页面内容上并**阻止与页面的交互**，直到它被关闭。

它与弹出框有类似的交互，但有两个主要区别：
- 它是模态的，并在对话框后面呈现一个背景，使后面的内容变暗，使页面的其余部分无法访问。
- 它在视口中居中，不锚定到任何特定的参考元素。

一个可访问的对话框组件具有以下要点：
- **`Dismissal`**：当用户按下  `esc`  键或在打开的对话框外按下时，它会关闭。
- **`Role`**：元素被赋予相关的角色和 ARIA 属性，以便屏幕阅读器可以访问。
- **`Focus management`**: 焦点完全被困在对话框中，必须由用户解除。

## Basic dialog

官方示例 👉 [CodeSandbox demo](https://codesandbox.io/s/stoic-bas-frzus0?file=/src/App.tsx)

此示例演示如何创建用于单个实例的对话框以熟悉基础知识。

让我们看一下这个例子：

### Open state

```jsx
import {useState} from 'react';
 
function Dialog() {
  const [isOpen, setIsOpen] = useState(false);
}
```

`isOpen` 确定对话框当前是否在屏幕上打开。它用于条件渲染。

### useFloating hook
[useFloating hook](https://floating-ui.com/docs/dialog#usefloating-hook)
 
`useFloating()` hook为我们的对话提供上下文。我们需要传递一些信息：
- `open` ：来自我们上面的 `useState()` 挂钩的打开状态。
- `onOpenChange`: 对话框打开或关闭时调用的回调函数。我们将使用它来更新我们的 `isOpen` 状态。

```jsx
import {useFloating} from '@floating-ui/react';
 
function Dialog() {
  const [isOpen, setIsOpen] = useState(false);
 
  const {refs, context} = useFloating({
    open: isOpen,
    onOpenChange: setIsOpen,
  });
}
```

### Interaction hooks
[Interaction hooks](https://floating-ui.com/docs/dialog#interaction-hooks)
-   `useClick()` 添加了在**单击引用元素**时**打开或关闭对话框**的功能。但是，对话框可能不会附加到引用元素，因此这是可选的。(一般对话框都是独立出来Portal的，也就是上下文是body)
- `useDismiss()` 添加了当用户按下 `esc` 键或在对话框外按下时关闭对话框的功能。可以将其的 `outsidePressEvent` 选项设置为 `'mousedown'` 以便触摸事件变得懒惰并且不会穿过背景，因为默认行为是急切的。（不太好理解，大概是）
- `useRole()` 将 `dialog` 的正确 ARIA 属性添加到对话框和引用元素。

最后， `useInteractions()` 将他们所有的 props 合并到 prop getters 中，然后就可以用于渲染。

```jsx
import {
  // ...
  useClick,
  useDismiss,
  useRole,
  useInteractions,
  useId,
} from '@floating-ui/react';
 
function Dialog() {
  const [isOpen, setIsOpen] = useState(false);
 
  const {refs, context} = useFloating({
    open: isOpen,
    onOpenChange: setIsOpen,
  });
 
  const click = useClick(context);
  const dismiss = useDismiss(context, {
    outsidePressEvent: 'mousedown',
  });
  const role = useRole(context);
 
  // Merge all the interactions into prop getters
  const {getReferenceProps, getFloatingProps} = useInteractions([
    click,
    dismiss,
    role,
  ]);
 
  // Set up label and description ids
  const labelId = useId();
  const descriptionId = useId();
}
```

### Rendering
[Rendering](https://floating-ui.com/docs/dialog#rendering)

现在我们已经设置了所有的变量和hook，可以渲染我们的元素了。
```jsx
function Dialog() {
  // ...
  return (
    <>
      <button ref={refs.setReference} {...getReferenceProps()}>
        Reference element
      </button>
      {isOpen && (
        <FloatingOverlay
          lockScroll
          style={{background: 'rgba(0, 0, 0, 0.8)'}}
        >
          <FloatingFocusManager context={context}>
            <div
              ref={refs.setFloating}
              aria-labelledby={labelId}
              aria-describedby={descriptionId}
              {...getFloatingProps()}
            >
              <h2 id={labelId}>Heading element</h2>
              <p id={descriptionId}>Description element</p>
              <button onClick={() => setIsOpen(false)}>
                Close
              </button>
            </div>
          </FloatingFocusManager>
        </FloatingOverlay>
      )}
    </>
  );
}
```
- `{...getReferenceProps()}` / `{...getFloatingProps()}`  上一篇说过，将 props 从交互挂钩传播到相关元素上。它们包含诸如 `onClick` 、 `aria-expanded` 等道具。
- `<FloatingOverlay />` 是一个在浮动元素后面**渲染背景覆盖元素**的组件，具有**锁定主体滚动**的能力。 [FloatingOverlay | Floating UI](https://floating-ui.com/docs/FloatingOverlay)
    - 它提供了一个固定的基本样式，使背景内容变暗并阻止浮动元素后面的指针事件。它是一个常规的<div/>，因此可以通过任何CSS解决方案进行样式设置。
- `<FloatingFocusManager />` 是一个管理和控制页面中浮动元素的焦点的组件，它能够自动检测焦点变化，并调整页面上的浮动元素的位置和状态，从而确保页面上所有元素的可访问性和可用性。默认情况通常**将焦点捕获在内部**。它应该**直接包裹浮动元素**，并且**只在对话框也被渲染时才被渲染**。 [FloatingFocusManager | Floating UI](https://floating-ui.com/docs/floatingfocusmanager)

补充：[FloatingPortal | Floating UI](https://floating-ui.com/docs/floatingportal)
- 将浮动元素传送到给定的容器元素中——默认情况下，在应用程序根之外并进入 body。
- 可以自定义 root，也就是可选择具有 id 的节点，或者创建它并将其附加到指定的根（body）

```jsx
import {FloatingPortal} from '@floating-ui/react';
 
function Tooltip() {
  return (
    isOpen && (
      <FloatingPortal>
        <div>Floating element</div>
      </FloatingPortal>
    )
  );
}
```

结合以上，