---
title: 浏览器缓存 local session
date: 2020-03-22 21:18:46
tags:
- 浏览器体系
- 网络
---

## what is it
<!--more -->
`Web` 存储对象 `localStorage` 和 `sessionStorage` 允许我们在浏览器上保存键值对。

这些键值对是什么呢？它们是网站留在浏览器里的数据，一般是地址，图片等。可以看做是已经浏览过的网页的副本。

## why is it

简单而又直至中心地说是为了提高上网速度，减轻各种服务器（域名解析，源主机，DNS等）的压力。至于为什么可以减轻，后面再写。

## LocalStorage & sessionStorage

### LocalStorage

`localStorage` 最主要的特点是：

- 同源(域名/协议/端口 三者都相同)的数据在所有浏览器标签页和窗口之间共享。
- 数据不会过期。它在浏览器重启甚至系统重启后仍然保留。

### sessionStorage

`sessionStorage` 的使用频率比 `localStorage` 少。

它具有更多的局限性：

- `sessionStorage` 的数据只存在于当前浏览器标签页。
- 具有相同页面的另外一个浏览器标签页中将会有不同的存储。
- 但是它在相同标签页下的 `iframes` 之间是共享的(假如它们是同源的)。
- 数据在页面刷新后仍然保留，但是在关闭重新打开浏览器标签页后不会被保留

### API

两者的`api`都是一致的：

- setItem(key, value) – 存储键值对。
- getItem(key) – 根据键名获取值。
- removeItem(key) – 删除单个数据。
- clear() – 删除所有数据。
- key(index) – 获取该索引下的键名。
- length – 存储数据的长度。
- 使用 Object.keys 获取所有的键。

### Storage 事件

- 在调用 `setItem`，`removeItem`，`clear`方法后触发。
- 返回包含有关操作（`key/oldValue/newValue`），文档 `url` 和存储对象（`storageArea`）的所有数据。
- 在除当前数据改变的对象以外所有能访问到存储对象的 `window` 对象上都会被触发（在 `sessionStorage` 有效范围的同一标签页下，在 `localStorage` 的有效范围下）。
- 可以实现同源的不同窗口交换消息
  
## 小结

简单认识了浏览器两种缓存机制，但都未深究，也没有非常具体的应用场景，下一篇出`session`来做个对比。
