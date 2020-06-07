---
path: Blog
date: 2020-06-07T03:20:10.584Z
title: Webpack `historyApiFallback`
description: 在使用 React Router DOM 搭配 Webpack 時遇到的錯誤與解決方法
---

在使用 React Router DOM 時遇到下面的錯誤：

> Cannot GET /user

而 console 裡報了這個 error:

> "Refused to load the font URL because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'font-src' was not explicitly set, so 'default-src' is used as a fallback."

原本去找了 `Content Security Policy` 的問題，但發現自己完全沒有設置 CSP 的 meta，因此開始懷疑 React Router....

發生的原因是因為，React Router 發的 router 並不是真實存在，也就是說 Webpack devServer 在看到這個 url 不知道要 Response 什麼回去

`devServer.historyApiFallback` 就是要解決這個問題，當設成 `true` 時，Webpack 不知道要回什麼 response 時，會回傳你預設的 `index.html`而不是 `404`，也可以傳入 object 自己設定 fallback

至於為什麼會有 CSP 的 issue，我在無痕模式就不會發生，所以應該是跟 chrome plugin 有關
