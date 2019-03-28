---
title: React 新生命周期
tags: [React, LifeCycle, API]
category: React
date: 2019-03-10 22:55
---

# React 新的生命周期

## 前言

前一段时间光注意`Hooks`来着，忽略了 React 16 这个版本对生命周期做了一个比较大的变更，所以接下来做一个整理，为将来出 BUG 做好准备（迫真）。
对了，生命周期整理完成以后，可能会整理整理`React.lazy`相关？

## 正文

先讲执行顺序，再讲新的生命周期。

### 执行顺序

#### 装载

这些是组件装载的时候执行的生命周期

1. constructor
2. static getDerivedStateFromProps()
3. render()
4. componentDidMount()

可以发现，`2` 这个东西是新多出来的，同时原来的`componentWillMount`已经被标记为`UNSAFE`，官方不推荐使用（当前版本不移除，可能在后续版本移除）

#### 更新

1. static getDerivedStateFromProps()
2. shouldComponentUpdate
3. render()
4. getSnapshotBeforeUpdate()
5. componentDidUpdate()

然后有这些被标记为`UNSAFE`:

- componentWillUpdate()
- componentWillReceiveProps()

#### 卸载

- componentWillUnmount()

#### 出错处理

- static getDerivedStateFromError()
- componentDidCatch()

### 新旧生命周期

#### constructor()

原来在构造函数中，我们一般只做两件事：

- 初始化`state`
- bind(this)

> Avoid copying props into state! This is a common mistake:

``` jsx
constructor(props) {
 super(props);
 // Don't do this!
 this.state = { color: props.color };
}
```

现在官方已经不推荐在构造函数中使用这种方式初始化`state`，其实对于这两种方法都可以在构造函数外面定义，所以可能构造函数的使用会慢慢减少。

#### static getDerivedStateFromProps()

这玩意是新加的一个静态方法，它会在第一次装载和更新（接收新的`props`或者父组件重新渲染）的时候调用，在这个静态方法里面访问不到`this`，它需要返回一个对象来表示需要更新的状态（如果没有更新则返回需要返回 null）

上面说了，它在接收新的`props`和父组件更新的时候调用，所以`this.setState`（我 更 新 我 自 己）的时候一般不会调用。

所以这个可以认为是被`UNSAFE`的`componentWillReceiveProps`的替代品，不过使用它的时候一般也需要对前后的属性进行比较，不过由于在静态方法内部拿不到`this`,如果想访问`this`的话，可以使用`getSnapshotBeforeUpdate`。

``` jsx
static getDerivedStateFromProps(nextProps, prevState) {
  if(nextProps.id === prevState.id) {
    return null;
  }

  return {
    name: nextProps.name,
    info: nextProps.info
  }
}
```

#### render()

忘了什么版本开始就支持数组和`Fragments`返回了，不用再套`div`了！（`Fragments`这个词一直记不住，所以我一直写`<></>`来着...）

``` jsx
render(){
 return
 //或者用[]或者用 <React.Fragments>
  <>
    <li>1</li>
    <li>2</li>
    <li>3</li>
 </>
}
```

#### getSnapshotBeforeUpdate()

这个是新增的生命周期（感觉新增加的名字都有点拗口），它会在 render 信息输出给`DOM`前之前调用，所以可以用它来记录这时`DOM`的信息，比如当前滚动条位置什么的，它返回一个对象，这个对象会作为`componentDidUpdate`的第三个参数。

我们可以把这个当做`componentWillUpdate`的替代。

``` jsx
  listRef = React.createRef();

  getSnapshotBeforeUpdate(prevProps, prevState) {
    if (prevProps.list.length < this.props.list.length) {
      return this.listRef.current.scrollHeight;
    }
    return null;
  }
  componentDidUpdate(prevProps, prevState, snapshot) {
    if (snapshot !== null) {
      this.listRef.current.scrollTop +=
        this.listRef.current.scrollHeight - snapshot;
    }
  }
```

#### static getDerivedStateFromError()

这个生命周期和`componentDidCatch`类似，不同的是，这个生命周期返回一个值去更新`state`表明出错了，而`componentDidCatch`木有返回，一般用于像服务器发送或记录错误。

``` jsx
  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
```

### 后记

可以看出新的生命周期扩展了之前的，使用变得更方便了，效率变得更高，如果你在组件中使用了新的生命周期，那么对应`UNSAFE`的会自动被禁用。
