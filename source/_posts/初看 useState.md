---
title: 初看 useState
tags: [React]
category: React
date: 2020-04-13 23:11
---
# 初看 useState

## 前言
React Hooks 中，useState 是最基础一个一个 API，一直想探究里面的具体实现，今天就让我康康！


## 总览
其实源码也就短短几行：
```javascript
export function useState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  const dispatcher = resolveDispatcher();
  return dispatcher.useState(initialState);
}
```

这个`dispatcher`是通过`ReactCurrentDispatcher`生成，最终指向了`ReactFiberHooks[.new|old].js`文件中，先看看 Hook 的类型定义：
```flow
export type Hook = {|
  // 指向当前渲染节点 Fiber, 上一次完整更新之后的最终状态值
  memoizedState: any,
  // 最初为起始的 State或每次更新是的新的 State
  baseState: any,
  // 当前需要更新的 Update
  baseQueue: Update<any, any> | null,
  // 缓存的更新队列，储存多次更新行为
  queue: UpdateQueue<any, any> | null,
  // 下一个 Hooks
  next: Hook | null,
|};
```
队列中涉及 React 调度行为，暂时没能力展开，可以看到 Hooks 是一个单向链表，每个 Hook 有 next 指向下一个 Hook。

## 执行

首先在首次渲染的时候（BeginWork），根据不同组件的类型执行不同的类型：
```javascript
  switch (workInProgress.tag) {
    case IndeterminateComponent: {
        //....
    }
    case LazyComponent: {
        // ....
    }
    case FunctionComponent: {
      const Component = workInProgress.type;
      const unresolvedProps = workInProgress.pendingProps;
      const resolvedProps =
        workInProgress.elementType === Component
          ? unresolvedProps
          : resolveDefaultProps(Component, unresolvedProps);
      return updateFunctionComponent(
        current,
        workInProgress,
        Component,
        resolvedProps,
        renderExpirationTime,
      );
    }
    case ClassComponent: {
        // .....  
    }
    // .... 还有很多判断
}
```
再往下到`updateFunctionComponent`，发现逻辑在`renderWithHooks`，省去各种东西发现了最初的赋值，分成初次渲染和更新：
```javascript
ReactCurrentDispatcher.current =
  current === null || current.memoizedState === null
    ? HooksDispatcherOnMount
    : HooksDispatcherOnUpdate
```

`HooksDispatcherOnMount`和`HooksDispatcherOnUpdate`中定义了`useState`分别为`mountState`和`updateState`，update 实际上是用 updateReducer 封装的，这里以`mountState`为例：
```javascript
function mountState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  // 这个函数创建 hook，如果是第一个就新建，否则放进 next 中
  const hook = mountWorkInProgressHook();
  if (typeof initialState === 'function') {
    // $FlowFixMe: Flow doesn't like mixed types
    initialState = initialState();
  }
  hook.memoizedState = hook.baseState = initialState;
  const queue = (hook.queue = {
    pending: null,
    dispatch: null,
    lastRenderedReducer: basicStateReducer,
    lastRenderedState: (initialState: any),
  });
  // 绑定当前的 fiber 和队列到 dispatch 上
  const dispatch: Dispatch<
    BasicStateAction<S>,
  > = (queue.dispatch = (dispatchAction.bind(
    null,
    currentlyRenderingFiber,
    queue,
  ): any));
  return [hook.memoizedState, dispatch];
}
```

再通过`dispatchAction`触发状态更新：
```javascript
try {
  const currentState: S = (queue.lastRenderedState: any);
  const eagerState = lastRenderedReducer(currentState, action);
  // Stash the eagerly computed state, and the reducer used to compute
  // it, on the update object. If the reducer hasn't changed by the
  // time we enter the render phase, then the eager state can be used
  // without calling the reducer again.
  update.eagerReducer = lastRenderedReducer;
  update.eagerState = eagerState;
  if (is(eagerState, currentState)) {
    // 相同就不更新了
    return;
  }
} catch (error) {
  // Suppress the error. It will throw again in the render phase.
}
```

## 总结

今天初步了解了 useState 的结构，其中很多还是涉及到调度相关，很多地方没能仔细了解，下次还得从 Component 看起..


