# 从 vue 的 scoped 属性开始

[toc]
最近在项目中遇到引入第三方组件库后，使用其组件对其进行样式覆盖后却并没有生效，但是直接将组件放在一个单文件组件中，修改的样式又会生效的现象。在一番查阅资料加水群后，终于我解决了这个问题，本文记录了问题产生的原因，解决思路以及关于 postcss 的一些知识。

## 一、vue 里的 scoped 属性

产生这个问题的原因是我使用了 style 标签中的 scoped 属性

```vue
<style scoped>
</style>
```

这个属性是HTML5的新属性，作用将 style 标签内的样式只应用于该 style 标签的父级元素和子元素。但是目前原生写的话，支持度够呛。
在 vue 单文件组件中，使用 scoped 的话，vue 通过在编译过程中为该组件所有标签自动添加一个 attribute（注意，这里只是该组件内部标签，不包括子组件内的标签，只是子组件的根节点会被添加。），并为该组件内的 css 选择器在末尾添加上这个属性选择器，为组件内部的 css 指定作用域，达到上述作用。更多请看 [官网](https://cn.vuejs.org/v2/guide/comparison.html#%E7%BB%84%E4%BB%B6%E4%BD%9C%E7%94%A8%E5%9F%9F%E5%86%85%E7%9A%84-CSS)

那了解了 scoped 属性的作用以及它的工作机制，我根据我前面给 class 设置 最高优先级的 `!important` 依然不生效的结果，我终于得出了就是这个 scoped 搞的鬼。（bgm起）事情的真相只有一个，在 vue 编译过程中，因为我使用了 scoped 属性，它给我的单文件组件内的所有标签都加上了一个`[data-v-hsah值]`的这样一个 attribute ，并且给我的 css 选择器默认在尾部添加一个属性选择器如：`.list-container[data-v-21e5b78]:hover`。但是子组件内也有自己的 scoped，也会产生自己的hash值，两组属性不一样，导致在我自己的组件内修改引用组件内部标签的样式时，也是直接选不到目的标签，所以我设置了最高优先级的 `!important`也是没用。

## 二、vue 解决 scoped 的思路

那么现在我已经知道问题是个名字对不上的问题，如何解决呢，很简单，就是让给我们目的标签套上我们的名字就好了。那我该如何给它下套呢？它是在编译后才定下的属性，编译前根本不能定位到它呀，除非找个占位符，占住位置，最后让父级元素的属性在这个地方替换掉占位符。

很自然的，官方也提供了这样的一个例外：在编写的 css 中，使用 `>>>` 来作为上述过程的占位符，最后被父级元素的属性替换，这就是官方的解决思路哒！（假如是一些不支持 `>>>` 语法的如 Sass 之类的预处理器，可以使用 `/deep/` 来代替）。

更多请查阅 [vue-loader](https://vue-loader.vuejs.org/zh/guide/scoped-css.html)

难道就一条解决思路吗？

那自然是不可能的，~~既然有人有问题，那我们把这个人解决了是不是就解决了问题呢？~~

秉持这个这个思路，既然我用来了 scoped 有问题，那我不用 scoped 不就好了。事实确实如此，只是这样会造成全局的命名污染和很麻烦的样式覆盖，故我在第一时间就排除了。

## 三、postcss

为什么会混入个奇怪的东西啊喂！

因为这是我在查阅资料的时候发现 vue 实现 scoped 就是靠 postcss 这个工具来增加属性和添加属性的。

其 [官方网站](https://www.postcss.com.cn/) 是这样介绍自己的：“PostCSS是一个用 JavaScript 工具和插件转换 CSS 代码的工具”。清晰明了，赞个。它提供一些基础功能如：添加前缀，使用先进特性，命名空间，语法检查等，除此之外还能写（引用）各种插件来满足自己的需求。

为了保持文章的专注度，现在我只需要知道它就是一个*用 JavaScript 来编译 css 为另一套 css 的工具*就好了，其他的原理性或者是配置性的就暂时按下不表。
