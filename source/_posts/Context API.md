---
title: Context API
tags: [React, Context]
category: React
date: 2018-07-09 22:25
---

# Context API

在 React 16.3 中，如果很多节点需要共享上下文数据，根节点提供所有需要的数据（provider），其他的节点不需要一层一层传递数据，可以直接从 Context API 获取数据（consumer），当数据变化的时候 React 会主动刷新

``` js

const enStrings = {
  submit: "Submit",
  cancel: "Cancel"
};

const cnStrings = {
  submit: "提交",
  cancel: "取消"
};
//创建Context，这个函数接收任意类型的数据，给默认值en
const LocaleContext = React.createContext(enStrings);

class LocaleProvider extends React.Component {
  state = { locale: cnStrings };
  toggleLocale = () => {
    const locale =
      this.state.locale === enStrings
        ? cnStrings
        : enStrings;
    this.setState({ locale });
  };
  render() {
    return (
    //使用Provider，其value值就是提供给消费者的内容
      <LocaleContext.Provider value={this.state.locale}>
        <button onClick={this.toggleLocale}>
          切换语言
        </button>
        //放children
        {this.props.children}
      </LocaleContext.Provider>
    );
  }
}

class LocaledButtons extends React.Component {
  render() {
    return (
    //消费者使用Context 的内容
      <LocaleContext.Consumer>
        {locale => (
          <div>
            <button>{locale.cancel}</button>
            &nbsp;<button>{locale.submit}</button>
          </div>
        )}
      </LocaleContext.Consumer>
    );
  }
}

export default () => (
  <div>
    <LocaleProvider>
      <div>
        <br />
        <LocaledButtons />
      </div>
    </LocaleProvider>
    //consumer一定要是provider的子元素，否则其获取的value值始终是开始定义的默认值
    <LocaledButtons />
  </div>
);

```
