---
title: React Portal
tags: [Javascript, React]
category: React
date: 2020-3-31 17:20
---
# React Portal

## 前言
在 React 中，一切皆是组件，但是会有这样一种场景，在某个子组件中，需要有一个模态/对话框的功能，这个弹框组件从业务逻辑来看应该是在`body`下，但是通过以往的方式往往只能使用 css 的方式达到类似的效果，同时还要把这个弹框组件作为通用组件，不免十分麻烦，那么如何才能实现这种效果？

```html
<body>
   <main></main>
   <Dialog />
</body>
```

## React Portal
Portals（传送门） 是 React 在 v16 推出的一个新的 API，它提供了一种将子节点渲染到存在于父组件以外的一种方案（即把子组件传送到父组件之外）。

React 在 v16 之前要实现这个方法提供了两个隐形的 API，不过相对来说也不够完美，直到 Portals。

Portal 用法十分简单：

```jsx
ReactDOM.createPortal(child, container);
// child 代表需要渲染的子节点
// container 代表了被挂载的元素
```

对于使用了 Portal 的情况来说，render 函数实际上没有渲染任何东西（相当于 null）：

```jsx
render() {
  // React 并*没有*创建一个新的 div。它只是把子元素渲染到 `domNode` 中。
  // `domNode` 是一个可以在任何位置的有效 DOM 节点。
  return ReactDOM.createPortal(
    this.props.children,
    domNode
  );
}
```

这样，虽然 portal 实际不在父元素的相关 DOM 中，但是它的 children 仍然在 React tree 中，仍然能够拿到上层的 context，岂不美哉？

下面是一个正常的 Modal 组件，在渲染的时候创建一个 div 组件，将其插入 body 中，然后在渲染时将 children 渲染到 div 下 ：
```jsx
class Modal extends React.Component {
  constructor(props) {
    super(props);
    this.el = document.createElement('div');
    document.body.appendChild(this.el);
  }

  componentDidMount() {
    modalRoot.appendChild(this.el);
  }

  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }
  
  render() {
    return ReactDOM.createPortal(
        this.props.children,this.el
    );
  }

```

## Portal 冒泡
既然在真实的 DOM 中 Portal 不在父元素之内，那么它的事件还会冒泡到父元素吗？
```jsx
<div onClick={handleClick}>
    <Modal>
        <Button>Click me</Button>
    </Modal>
</div>
```
答案是会的，在上面的例子中，Modal 的 children 并没有绑定 click 事件，但是点击按钮的时候，事件依然会从传送门中传到 onClick 上。

## 总结
React Portal 提供了一种非常好的创建模态/对话框的功能，从而避免了之前繁琐甚至还可能导致其他问题的创建方法，不由得让我赞叹一声：老哥稳！

## 参考
- [React Portals](https://zh-hans.reactjs.org/docs/portals.html)
- [传送门：React Portal](https://zhuanlan.zhihu.com/p/29880992)