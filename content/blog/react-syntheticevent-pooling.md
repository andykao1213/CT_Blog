---
path: blog
date: 2020-06-13T14:57:40.478Z
title: React SyntheticEvent Pooling
description: 在使用React event handling時踩到一個意想不到的雷....
---
## 問題

---

今天我想用React做一個讓使用者填email, 名字的表單，我可能會這樣做：

```jsx
function Form() {
  const [formData, setFormData] = useState({ email: "", name: "" });

  function handleChange(e) {
    setFormData(prev => ({ ...prev, [e.target.name]: e.target.value }));
  }

  return (
      <form>
        <input
          name="email"
          value={formData.email}
          onChange={handleChange}
          type="email"
          placeholder="Email"
        />
        <input
          name="name"
          value={formData.name}
          onChange={handleChange}
          type="name"
          placeholder="Name"
        />
      </form>
  );
}
```
[codesandbox](https://codesandbox.io/s/syntheticevent-v81of?file=/src/App.js)

然而，當handleChange被觸發時，會有以下的error

```
Cannot read property 'name' of null
```

## 原因

---

我們看到 `handleChange` 收到的 event 其實並不是 DOM 原生的 event, 而是由 React “合成” 的 `SyntheticEvent` 。他的 api 基本上和原生的 event 一致，大部分的使用狀況都ㄧ樣，唯獨一個特色 - Event Pooling.

因為performance的緣故，React 會在event callback結束時，把整個event 的properties nullish，放便下次重用。因此如果在async的狀況下取得event, 他就會是被nullish過的，這也就是為什麼上面的錯誤會出現。

要解決上面的錯誤，可以不要使用下面兩種方式：

不要傳入callback

```jsx
function handleChange(e) {
  setFormData({ ...formData, [e.target.name]: e.target.value });
}
```

用一個新的變數去接想要得值

```jsx
function handleChange(e) {
	const {name, value} = e.target;
  setFormData(prev => ({ ...prev, [name]: value }));
}
```

如果 `handleChange` 需要包 `useCallback` 的時候，後者可以減少depedency。因此我會傾向用後者

## Reference

---

[SyntheticEvent - React](https://reactjs.org/docs/events.html#event-pooling)
