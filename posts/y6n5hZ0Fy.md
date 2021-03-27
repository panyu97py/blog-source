---
title: 'electron+sqlite3'
date: 2021-02-23 10:36:20
tags: [electron]
published: true
hideInList: false
feature: 
isTop: false
---
### 安装
```
yarn add sqlite3
```

### 开启`node`集成，很重要！！
> 两种方式，如不开启会遇到`Module not found: Can't resolve 'aws-sdk'`和`Cannot read property '_handle' of undefined`

#### 1.通过 `vue-cli-plugin-electron-builder` 插件生成的`electron`
> 通过修改 `Vue.config.js`,修改主进程中的配置.

```
const path = require('path')
module.exports = {
    // ···
    pluginOptions: {
        electronBuilder: {
            nodeIntegration: true//增加或修改此处
        }
    }
      // ···
}

```
#### 2.直接修改主进程
```js
//...
async function createWindow() {
    // Create the browser window.
    const win = new BrowserWindow({
        width: 800,
        height: 600,
        webPreferences: {

            // Use pluginOptions.nodeIntegration, leave this alone
            // See nklayman.github.io/vue-cli-plugin-electron-builder/guide/security.html#node-integration for more info
         nodeIntegration:true// 修改或增加此处
        }
    })

    if (process.env.WEBPACK_DEV_SERVER_URL) {
        // Load the url of the dev server if in development mode
        await win.loadURL(process.env.WEBPACK_DEV_SERVER_URL)
        if (!process.env.IS_TEST) win.webContents.openDevTools()
    } else {
        createProtocol('app')
        // Load the index.html when not in development
        win.loadURL('app://./index.html')
    }
}
//...
```