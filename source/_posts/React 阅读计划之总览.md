---
title: React 阅读计划之总览
tags: [React]
category: React
date: 2020-04-06 15:10
---
# React 阅读计划之总览

## 前言
开新坑！之前看 React 源码都只是部分看，没有详细剖析，这次希望从 React core 开始，一点一点理解整个项目的核心，这可能是一个周期特别长的事情，希望我能坚持下去。

## monorepo
React 使用了 monorepo 的形式，即将强关联的多个项目放在一个存储库中，与之相对的叫 multirepo(manyrepo)，这两种形式没必要一定分个高低，找到合适的形式就好了。

## 目录形式
先看目录形式及其用途吧（部分文件夹没有 README，所以用途暂时猜测，之后再补充）
```
react
├── fixtures ---------------------------------------- 开发的一些测试用例
├── packages ---------------------------------------- 主要源码目录 
│   ├── create-subscription ------------------------- 组件中订阅额外数据的工具
│   ├── dom-event-testing-library ------------------- 单元测试 DOM 事件的库
│   ├── eslint-plugin-react-hooks ------------------- ESLint Hooks 相关
│   ├── jest-mock-scheduler ------------------------- jest 测试 scheduler 的工具
│   ├── jest-react ---------------------------------- jest 测试 React 渲染器工具
│   ├── legacy-events ------------------------------- 事件相关
│   ├── react --------------------------------------- React 组件核心
│   ├── react-art ----------------------------------- React 绘制矢量图形相关
│   ├── react-cache --------------------------------- React 基础应用缓存
│   ├── react-client -------------------------------- React 自定义流模型
│   ├── react-debug-tools --------------------------- React debug 渲染器工具
│   ├── react-devtools ------------------------------ React devtool 开发工具
│   ├── react-devtools-core ------------------------- devtool 浏览器外的核心实现
│   ├── react-devtools-extensions ------------------- devtool 浏览器插件
│   ├── react-devtools-inline ----------------------- 基于浏览器 IDE 的 devtool 实现
│   ├── react-devtools-shared ----------------------- devtool 公共代码
│   ├── react-devtools-shell ------------------------ devtool 测试工具
│   ├── react-dom ----------------------------------- React DOM 核心
│   ├── react-flight-dom-relay ---------------------- React Flight
│   ├── react-flight-dom-webpack -------------------- webpack 实现 react flight 绑定
│   ├── react-interactions -------------------------- 内部测试使用
│   ├── react-is ------------------------------------ React 元素类型
│   ├── react-native-renderer ----------------------- React Native 渲染
│   ├── react-noop-renderer ------------------------- React Fiber 测试相关
│   ├── react-reconciler ---------------------------- React reconciler 核心
│   ├── react-refresh ------------------------------- 热更新相关
│   ├── react-server -------------------------------- 服务器渲染相关
│   ├── react-test-renderer ------------------------- React 实验性渲染器
│   ├── scheduler ----------------------------------- 协作调度相关
│   ├── shared -------------------------------------- 通用代码
│   └── use-subscription ---------------------------- 管理订阅 Hooks
└── scripts ----------------------------------------- 脚本代码

```

## 核心导出
再来看看 React core 导出了哪些内容：
```javascript
export {
  Children,
  createRef,
  Component,
  PureComponent,
  createContext,
  forwardRef,
  lazy,
  memo,
  useCallback,
  useContext,
  useEffect,
  useImperativeHandle,
  useDebugValue,
  useLayoutEffect,
  useMemo,
  useReducer,
  useRef,
  useState,
  useMutableSource,
  createMutableSource,
  Fragment,
  Profiler,
  StrictMode,
  Suspense,
  createElement,
  cloneElement,
  isValidElement,
  version,
  __SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED,
  createFactory,
  useTransition,
  useDeferredValue,
  SuspenseList,
  unstable_withSuspenseConfig,
  block,
  DEPRECATED_useResponder,
  DEPRECATED_createResponder,
  unstable_createFundamental,
  unstable_createScope,
} from './src/React';
```
一下简要介绍一下可以使用的，废弃和不建议使用的就不提了

> __SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED



### Children
提供了五个方法来处理 children tree，包括
  - map，遍历方法，返回数组，类似`Array.prototype.map`
  - forEach，遍历方法，不返回数组，类似`Array.prototype.forEach`
  - count，计数，用 map 计算 children 中组件数量，类似`Array.length`
  - toArray，将 children 数据结构以数组形式展开并返回
  - only，判断 children 是否只有一个元素

### Ref 相关
有两个方法和 Ref 相关，分别是：
- `createRef`，创建 ref 函数
- `forwardRef`，传递 ref

### Component 相关
- Component 组件
- PureComponent 浅比较纯组件

### 异步加载相关
- lazy 实现异步加载模块
- Suspense 使用异步加载时的提示

### Hooks 相关
- `useState`，状态管理
- `useEffect`，生命周期管理，浏览器更新 DOM 之后异步调用
- `useLayoutEffect`，类似`useEffect`，浏览器更新 DOM 之后同步调用
- `useContext`，context 管理
- `useCallback`，函数缓存
- `useMemo`，缓存函数结果
- `useReducer`，类 reducer 管理状态
- `useRef`，ref 管理
- `useImperativeHandle`，使用 ref 时自定义暴露给父组件的实例值，应和`forwardRef`一起使用
- `useDebugValue`，在 devtool 中显示自定义 hook
- `useMutableSource`，[新 API](https://github.com/facebook/react/pull/18000)，使组件可以在并发模式下安全有效地从可变的外部源读取数据
- `useTransition`，Concurrent 模式不稳定功能，允许组件在切换到下一个界面之前等待内容加载
- `useDeferredValue`，Concurrent 模式不稳定功能，返回一个延迟更新的状态，更新前可在后台渲染

### Element 相关
- `createElement`，创建 React 元素
- `cloneElement`，克隆 React 元素，其中 props 是浅复制
- `isValidElement`，是否是一个 React 元素

### 其他
- `Fragment`，使 render 顶层可以有多个元素
- `Profiler`，性能测试
- `StrictMode`，严格模式
- `version`，React 版本

## 参考
- [官方文档](https://reactjs.org/docs/)

