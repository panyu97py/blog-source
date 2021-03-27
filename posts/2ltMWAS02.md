---
title: 'scss/sass 全局变量配置'
date: 2021-02-25 11:25:42
tags: []
published: false
hideInList: true
feature: 
isTop: false
---
### [sass-resources-loader](https://www.npmjs.com/package/sass-resources-loader)

#### 安装

```bash
npm install sass-resources-loader
```

#### 全局变量文件

```scss
/* resources.scss */
 
$section-width: 700px;
 
@mixin section-mixin {
  margin: 0 auto;
  width: $section-width;
}
```

#### 配置loader

```js
{
    loader: 'sass-resources-loader',
    options: {
        resources: 'example/a.scss'
    }
}
```