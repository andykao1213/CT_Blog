---
path: Blog
date: 2020-06-07T03:20:10.584Z
title: Webpack `historyApiFallback`
description: 在使用 React Router DOM 搭配 Webpack 時遇到的錯誤與解決方法
---
在使用React Router DOM時遇到下面的錯誤：

> Cannot GET /user



而console裡報了這個error:

> Refused to load the font '<URL>' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'font-src' was not explicitly set, so 'default-src' is used as a fallback.

原本去找了 `Content Security Policy` 的問題，但發現自己完全沒有設置CSP的meta，因此開始懷疑 React Router....

發生的原因是因為，React Router發的router並不是真實存在，也就是說Webpack devServer在看到這個Url不知道要Response什麼回去

`devServer.historyApiFallback` 就是要解決這個問題，當設成 `true` 時，Webpack不知道要回什麼response時，會回傳你預設的 `index.html`而不是 `404`，也可以傳入object自己設定fallback

至於為什麼會有CSP的issue，我在無痕模式就不會發生，所以應該是跟chrome plugin有關
