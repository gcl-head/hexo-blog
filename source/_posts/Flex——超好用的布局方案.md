---
title: Flex——超好用的布局方案
date: 2019-09-20 18:08:10
updated: 2019-09-20 18:08:10
tags:
    - css
    - flex
categories: frontend
---
> 对齐、居中是我写前端的时候最头疼的两件事情，要实现这两个功能，还要保证一定的兼容性是很复杂的事情，网上有很多实现对齐的文章，让人看得头晕目眩。
>
> 有了Flex，一切都不一样了，我们再也不用把生命浪费在‘垂直居中’上了！

## 使用flex布局

```css
.xxx{
  display: flex;
}
.xxx{
  display: inline-flex; /* 行内也可以使用flex布局 */
}
```

## 容器属性

| 属性            | 含义         | 值                                                           | 常用值                                         |
| --------------- | ------------ | ------------------------------------------------------------ | ---------------------------------------------- |
| flex-direction  | 项目排列方向 | row(默认)、row-reverse、column、column-reverse               | row / column                                   |
| flex-wrap       | 项目是否换行 | nowrap(默认)、wrap、wrap-reverse                             | wrap（允许换行）                               |
| justify-content | 水平对齐方向 | flex-start(默认)、flex-end、center、space-between、space-around | center(**水平居中**)/ space-around(等间距布局) |
| align-items     | 垂直对齐方向 | flex-start、flex-end、center、baseline、stretch(默认: 占满整个容器的高度) | center(**垂直居中**)                           |

## 项目属性

| 属性       | 含义                                                         | 值                                              | 常用值 |
| ---------- | ------------------------------------------------------------ | ----------------------------------------------- | ------ |
| order      | 项目排列顺序，越小越靠前                                     | 0(默认)、1、2...                                | 整数   |
| flex       | `flex-grow`, `flex-shrink` 和 `flex-basis`的简写，<br />后两个属性可选，一般只用第一个属性，表示项目<br />放大比例，类似栅格。 | 0 0 auto(默认)...                               | 整数   |
| align-self | 允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性 | flex-start、flex-end、center、baseline、stretch |        |