---
title: 'VUE CLI3 结合 cross-env 配置环境变量'
date: 2021-03-27 18:25:05
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
```
'use strict'
const merge = require('webpack-merge')
const prodEnv = require('./prod.env')

module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  API_ENV: `"${process.env.API_ENV}"`
})

```