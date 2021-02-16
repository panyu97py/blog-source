---
title: 'ios css border-radius元素 overflow:hidden失效问题'
date: 2021-02-17 00:49:36
tags: [css]
published: true
hideInList: false
feature: 
isTop: false
---
## 问题
父级设置圆角属性失效 父元素使用border-radius和overflow:hidden做成圆形，子元素如果使用了transform属性，则父元素的overflow:hidden会失效。

## 解决
```
-webkit-backface-visibility: hidden;
-webkit-transform: translate3d(0, 0, 0);
```