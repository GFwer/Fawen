---
title: 初探 React Fiber
tags: [React]
category: React
date: 2020-04-12 20:33
---
# 初探 React Fiber

## 前言
一直想深入了解一下 React Fiber 的东西，今天终于有机会来看看了，也为以后看相关的代码做一个准备吧。

## 开始
Fiber 体系有两个主要的阶段：渲染（render）和提交（commit）。

渲染阶段主要做以下事情：
- 遍历组件树
- 更新 state 和 props
- 调用生命周期函数
- 从组件中找到 children，和之前的 children 进行比较
- 找到需要自行的 DOM 更新

同时根据不同的组件类型可能执行不同类型的工作（Class Component 需要实例化，Function Component 则不需要）

不过这里的问题在于，如果一次有太多更新操作需要处理，那么可能就会造成阻塞掉帧，每一次更新，React 都要同步遍历并为每一个组件执行以上工作，如果组件树很庞大，那么处理时间可能就会大于`1/60 = 16.67 ms`，导致用户界面遭到阻塞。

那么有什么解决方法呢？

`requestIdleCallback`，一帧之中要做的事情按顺序可以如下表示：
- 用户事件交互
- JavaScript 执行处理
- requestAnimationFrame
- 布局（重排）
- 绘制（重绘）

如果在执行完成了这些东西还没到 16.67ms ，也就是说现在浏览器已经做完了所有工作，它就等待这一帧的时间到了就可以内容更新在显示器上了，这段空闲的时间资本家当然不能放过，他们指定了一个`requestIdleCallback`函数，告诉浏览器，如果你还有剩余时间就执行函数里面的内容。

```javascript
requestIdleCallback(()=>{
    // do something
})
```
现在 React 已经不使用这个函数了，React 在 Scheduler 中实现了自己的调度算法，不过核心思路和`requestIdleCallback`类似。

简单来说，在事件循环中，如果仅仅依靠调用栈，那么调用栈将一直执行到空未知，这时候可能出现失去同步问题，所以 React Fiber 的核心思想就是将同步的递归模型迁移到异步的链表指针模型上，它相当于一个可控的调用栈，随时可以中断。


## Fiber 
### React 节点
首先有下面的`ClickCounter`组件代码：
```jsx
<button key="1" onClick={this.onClick}>Update counter</button>
<span key="2">{this.state.count}</span>
```
经过 JSX 编译会返回两个 React 元素，实际上也可以这么写：
```javascript
class ClickCounter {
    ...
    render() {
        return [
            React.createElement(
                'button',
                {
                    key: '1',
                    onClick: this.onClick
                },
                'Update counter'
            ),
            React.createElement(
                'span',
                {
                    key: '2'
                },
                this.state.count
            )
        ]
    }
}
```
在 render 中调用`createElement`会返回两个数据结构：
```javascript
[
    {
        $$typeof: Symbol(react.element),
        type: 'button',
        key: "1",
        props: {
            children: 'Update counter',
            onClick: () => { ... }
        }
    },
    {
        $$typeof: Symbol(react.element),
        type: 'span',
        key: "2",
        props: {
            children: 0
        }
    }
]
```
其中`$$typeof`是唯一标识。

### Fiber 节点
在 render 阶段，上面产生的 React 元素的数据都会被合并到 Fiber tree 中，这意味着每个 React 元素都会有一个相应的 Fiber 节点，不过和 React 元素不同，每次重新渲染不会重新创建，这意味着 Fiber tree 是可变的数据结构，每次更新 React 都会利用 React 元素的属性来更新 Fiber 节点，如果某一次更新没有返回对应的元素，Fiber tree 还会根据 key 来做移动或者删除。

所有的 Fiber 节点都通过链表链接起来。

### current tree 和 workInProgress tree
在第一次渲染之后，React 就得到了一棵 Fiber tree，它反映了用于渲染 UI 的应用程序的状态，这棵树通常被称为 current 树，当 React 处理更新的时候，它会构建一棵 workInProgress 树，它反映了未来的 UI 状态。

为什么要有 workInProgress tree，因为 React 总会一次性来更新 UI，如果在 Fiber tree 上更新，可能会有割裂的效果，于是先构建一个草稿，所有的修改都在草稿上进行，修改完成把这个树呈现在屏幕上，这时候 workInProgress tree 就转化成了 current tree。

### effectTag
当 state 或者 props 变化的时候，会引起重新渲染，这个过程叫做 side effect，side effect 有很多种情况，而`effectTag`通过二进制记录了具体执行哪一种 effect：
```javascript
// Don't change these two values. They're used by React Dev Tools.
export const NoEffect = /*              */ 0b0000000000000;
export const PerformedWork = /*         */ 0b0000000000001;

// You can change the rest (and add more).
export const Placement = /*             */ 0b0000000000010;
export const Update = /*                */ 0b0000000000100;
export const PlacementAndUpdate = /*    */ 0b0000000000110;
export const Deletion = /*              */ 0b0000000001000;
export const ContentReset = /*          */ 0b0000000010000;
export const Callback = /*              */ 0b0000000100000;
export const DidCapture = /*            */ 0b0000001000000;
export const Ref = /*                   */ 0b0000010000000;
export const Snapshot = /*              */ 0b0000100000000;
export const Passive = /*               */ 0b0001000000000;
export const Hydrating = /*             */ 0b0010000000000;
export const HydratingAndUpdate = /*    */ 0b0010000000100;

// Passive & Update & Callback & Ref & Snapshot
export const LifecycleEffectMask = /*   */ 0b0001110100100;

// Union of all host effects
export const HostEffectMask = /*        */ 0b0011111111111;

export const Incomplete = /*            */ 0b0100000000000;
export const ShouldCapture = /*         */ 0b1000000000000;
```

### nextEffect
通过 nextEffect 可以实现快速迭代，每个 Fiber 节点都有一个 nextEffect（第一个是 firstEffect）指向下一个关联节点，这样每次就不用遍历全部节点，只需要遍历影响到的节点就行了

遍历前
![nextEffect](https://static.gongfangwen.com/2020-04-12-15866925028106.jpg)
链表直接遍历
![](https://static.gongfangwen.com/2020-04-12-15866926217095.jpg)


### Fiber 节点
之前`span`的 Fiber 节点就是这样：
```javascript
{
    stateNode: new HTMLSpanElement,    // 对 DOM 或者组件的引用
    type: "span",                      // 元素类型
    alternate: null,                   // workInProgress tree 节点引用
    key: "2",                          // 唯一的 key 值
    updateQueue: null,                 // 用于状态更新，回调函数，DOM 更新的队列
    memoizedState: null,               // Fiber 状态，更新的时候表示当前屏幕上的状态
    pendingProps: {children: 0},       // 在更新的时候存着新的 Props
    memoizedProps: {children: 0},      // 在更新的时候存着旧的 Props
    tag: 5,                            // 区分 Fiber 不同类型
    effectTag: 0,                      // 标记节点需要进行哪些更新
    nextEffect: null                   // 指向同次更新的下一个节点
}
```

## Render && Commit
render 阶段通过 setState 或者 render 来执行组件的更新，确定 UI 中要更新的内容，更新 Fiber tree（第一次为创建），输出一个有部分节点标记了 effect 的 Fiber tree（workInProgress tree）。

Commit 阶段，接收上一个阶段生成的 Fiber tree，遍历 effect 节点并执行更改。

render 阶段是**可以异步**执行的，React 更改了一部分 Fiber 节点后可以停止处理，让出调度权给优先级更高的事件，处理完成之后从停下的地方继续处理（也可能重新处理），由于这个阶段并不会表现在屏幕上，所以不会有问题，render 阶段始终会从 root 节点开始走起，跳过那些不需要处理的节点（或者上次处理过的），直达需要处理的节点，这些是在 render 阶段执行的生命周期（他们可能执行两次）：
- `getDerivedStateFromProps`
- `shouldComponentUpdate`
- `render`

Commit 阶段**必须同步**执行，因为它会生成用户可见的变化，这些是在 commit 阶段执行的生命周期：
- `componentDidMount`
- `getSnapshotBeforeUpdate`
- `componentDidUpdate`
- `componentWillUnmount`

## 后记
React Fiber 内容确实有点庞大，今天看了一下也有许多新的认识，希望我以后真正看代码的时候不要慌（捂脸

## 参考
翻译：
- [Build your own React](https://pomb.us/build-your-own-react/)
- [Inside Fiber: in-depth overview of the new reconciliation algorithm in React](https://indepth.dev/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react/)
- [The how and why on React’s usage of linked list in Fiber to walk the component’s tree](https://indepth.dev/the-how-and-why-on-reacts-usage-of-linked-list-in-fiber-to-walk-the-components-tree/)