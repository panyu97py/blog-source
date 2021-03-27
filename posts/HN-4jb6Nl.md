---
title: 'Vue+Electron项目搭建'
date: 2021-02-23 10:23:33
tags: [electron]
published: true
hideInList: false
feature: 
isTop: false
---
### 创建项目

```bash
vue create myproject
```

### 进入项目目录
```bash
cd myproject
```

### 安装`vue`插件`electron-builder`
```bash
vue add electron-builder
```

### 运行项目
```bash
yarn electron:serve
```

### 修改目录结构
> 根目录下创建 `Vue.config.js` 文件

```
├─.gitignore
├─README.md
├─Vue.config.js
├─babel.config.js
├─dataBase.db
├─package.json
├─webpack.config.js
├─yarn.lock
├─src
|  ├─renderer
|  |    ├─App.vue
|  |    ├─main.js
|  |    ├─views
|  |    ├─utils
|  |    ├─router
|  |    ├─components
|  |    └assets
|  ├─main
|  |  └index.js
├─public
|   ├─favicon.ico
|   └index.html
```
```js
const path = require('path')
module.exports = {
    pages: {
        index: 'src/renderer/main.js',//vue入口文件，即 electron renderer进程入口
    },
    pluginOptions: {
        electronBuilder: {
            mainProcessFile: 'src/main/index.js',// electron mian 进程入口
            mainProcessWatch: ['src/main'],
            nodeIntegration: true,//是否启用node交互
            // [1.0.0-rc.4+] Provide a list of arguments that Electron will be launched with during "electron:serve",
            // which can be accessed from the main process (src/background.js).
            // Note that it is ignored when --debug flag is used with "electron:serve", as you must launch Electron yourself
            mainProcessArgs: []
        }
    },
    configureWebpack: {
        resolve: {
            alias: {
                "@": path.join(__dirname, 'src')//别名配置
            }
        }
    }
}
```