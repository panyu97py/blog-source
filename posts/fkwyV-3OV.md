---
title: 'React-Native 安卓 文字截断 文字看不全 解决方案'
date: 2021-02-17 00:08:16
tags: [react-native]
published: true
hideInList: false
feature: 
isTop: false
---

### 保证每个Text组件, RN > 0.60, 小于这个版本可能不存在这个问题

1. 基本样式都有 `fontFamily: ''` , 你没看错, 至少得有个字体为空, 也不影响用户自定义字体

2. 属性至少有 `textBreakStrategy='simple'`

3. 属性至少有 `numberOfLines={0}`, 意思就是你不设置也要搞个初始值0
