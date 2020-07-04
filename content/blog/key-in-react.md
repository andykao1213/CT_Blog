---
path: blog
date: 2020-07-04T06:39:15.623Z
title: Key in React
description: 深入了解為什麼需要在key prop在React的作用
---

今天面試被問到了這個經典的問題，覺得答的沒有很好，在這邊再整理一次。

## Key 的使用場景

---

首先，key 的使用場景是在你用 map render 一系列相同的 element，範例如下：

```jsx
const listOfName = ['Andy', 'Bob', 'Cindy', 'David'];
listOfName.map(name => <p key={/* put key here */}>{name}</p>);
```

如果不提供 key 的話，react 會以 element 在 array 的 index 作為 key，並給 warning。

## Bug 範例

---

React 為什麼要針對這件事來做 warning，我們可以看下面的例子：

```jsx
function Component() {
  const [listOfName, setNames] = useState(["Andy", "Bob", "Cindy", "David"])

  const onInsert = () => {
    setNames(prev => ["Elva", ...prev])
  }

  return (
    <>
      <ul>
        {listOfName.map(name => (
          <li>
            <label for="some input">{name}</label>
            <input />
          </li>
        ))}
      </ul>
      <button onClick={onInsert}>Add Elva</button>
    </>
  )
}
```

也可以在[code sandbox](https://codesandbox.io/s/react-key-issue-9gfte?file=/src/App.js:65-506) 直接看

重現錯誤的步驟

1. 在每個 input 輸入一些文字
2. 按下 _Add Elva_

預期結果：

新增一個 Elva 以及空白的輸入框在第一個

實際結果：

Elva 的輸入框帶有原本 Andy 輸入框的值，Andy 帶有 Bob 的值....以此類推

## 一切都是 Reconcilation

---

我們知道，當 React State Change 後會做下面三件事

1. Rerender (Trigger function component or `render` function in class component)
2. Reconcilation (diff)
3. Commit Change to DOM

key 最主要的作用是在 Reconcilation 時，去判斷這次 state change 要提交哪些變化。

底下是我們的心理模型（以下都用 `ListItem` 取代 `<li><label>....`）：

```diff
+ <ListItem key={0} label="Elva" />
  <ListItem key={1} label="Andy" />
  <ListItem key={2} label="Bob" />
  <ListItem key={3} label="Cindy" />
  <ListItem key={4} label="David" />
```

但 React 會以 `key` 判斷是否為同一個 element，因此 React 的模型會是這樣：

```diff
-	 <ListItem key={0} label="Andy" />
+  <ListItem key={0} label="Elva" />
-	 <ListItem key={1} label="Bob" />
+	 <ListItem key={1} label="Andy" />
-	 <ListItem key={2} label="Cindy" />
+	 <ListItem key={2} label="Bob" />
-	 <ListItem key={3} label="David" />
+	 <ListItem key={3} label="Cindy" />
+	 <ListItem key={4} label="David" />
```

對 React 來說，不是在頭的地方加入 `Elva` ，而是 `Andy` 改叫 `Elva` ， `Bob` 改叫 `Andy` 以此類推

## 解決方法

---

給一個 unique id 當 key 吧！

```diff

 function Component() {
  const [listOfName, setNames] = useState(["Andy", "Bob", "Cindy", "David"]);

  const onInsert = () => {
    setNames(prev => ["Elva", ...prev]);
  };

  return (
    <>
      <ul>
        {listOfName.map(name => (
-          <li>
+          <li key={name}>
            <label for="some input">{name}</label>
            <input />
          </li>
        ))}
      </ul>
      <button onClick={onInsert}>Add Elva</button>
    </>
  );
}
```

當然，這邊也要確保 name 不會重複。可以根據實際情況使用 DB 的 id 或是自己 gen uuid 等等
