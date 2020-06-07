---
path: blog
date: 2020-06-07T06:29:46.967Z
title: React Component Mounting
description: Function Component Mounting的行為...
---
## 問題
---
前幾天發現底下這種 pattern 會讓 `Child` 在每次 `Parent` rerender 都會重新mount

```jsx
function Parent(){
  function Child(){
    return <h1>Hello React</h1>
  }
  return <Child />
}
```

但這種就不會

```jsx
function Parent(){
  function Child(){
    return <h1>Hello React</h1>
  }
  return Child()
}
```

## 原因
---
當我們寫成Jsx時，其實等價於下面

```jsx
function Parent(){
  function Child(){
    return <h1>Hello React</h1>
  }
  return React.createElement(Child, [], []);
}
```

而因為每次rerender, 都相當於重新 trigger 一次 `Parent` 這個function, 因此會重新建造一個execution context, 故 `Child` 在每次都是不同的function, 因此被視為不同的component, React就重新mount一次

然而第二種做法，只是單純的function, 不會create component，所以實際上等價於下面

```jsx
function Parent(){
  return <h1>Hello React</h1>
}
```

因此不會有重新mount的行為。更精確來說， `Child` component 根本不存在， `Child` 只是一個function.
