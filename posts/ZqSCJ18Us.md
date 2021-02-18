---
title: 'react native webview 的使用'
date: 2021-02-18 11:24:13
tags: [react-native]
published: true
hideInList: false
feature: 
isTop: false
---
#### 安装 
```bash 
yarn add react-native-webview 
``` 
#### 渲染远程 HTML 内容 
```
import React, { Component } from 'react'; 
import { View } from 'react-native'; 
import { WebView } from 'react-native-webview'; 
export default class App extends Component { 
    render(){
        return (
            <View>
                <WebView source={{ uri: 'https://www.baidu.com' }} /> 
            </View>
        )
    }
} 
```
#### 渲染 HTML 字符串
```
<WebView 
originWhitelist={['*']} 
source={{ html: `<h1>这里是一个标题</h1>` }} 
/> 
``` 
#### 根据内容自动计算高度 
往往我们无法提前预知 `HTML` 内容的高度，所以我们无法给其设置一个固定的高度，而是需要根据内容来设置其高度。

为了达到这个目的，我们需要用到 `injectedJavaScript` 和 `onMessage`

##### injectedJavaScript
这个属性的作用是设置一个在网页加载之前执行的 js 代码。

##### onMessage

在 `webview` 内部的网页中调用 `window.postMessage` 方法时可以触发此属性对应的函数，从而实现网页和 `RN` 之间的数据交换。 设置此属性的同时会在 `webview` 中注入一个` postMessage` 的全局函数并覆盖可能已经存在的同名实现。

网页端的 `window.postMessage` 只发送一个参数 `data`，此参数封装在 `RN` 端的 `event` 对象中，即 `event.nativeEvent.data`。`data` 只能是一个字符串。

```
import React, { Component } from 'react';
import { View, ScrollView, SafeAreaView } from 'react-native';
import { WebView } from 'react-native-webview';

export default class App extends Component {
  constructor(props) {
    super(props);

    this.state = { webViewHeight: 0 };
  }

  {/* 根据内容计算 WebView 高度 */}
  onWebViewMessage = (event) => {
    this.setState({ webViewHeight: Number(event.nativeEvent.data) });
  }

  render() {
    const html = `<p>这里是一个标题</p>`
    const injectedJavaScript = `window.ReactNativeWebView.postMessage(document.documentElement.scrollHeight)`
    return (
      <View style={{ flex: 1 }}>
        <View style={{ height: 100, backgroundColor: 'yellow' }}></View>
        <View style={{ height: this.state.webViewHeight }}>
          <WebView
            originWhitelist={['*']}
            source={{ html }}
            injectedJavaScript={{ injectedJavaScript }}
            onMessage={this.onWebViewMessage}
          />
        </View>
        <View style={{ height: 100, backgroundColor: 'green' }}></View>
      </View>
    )
  }
}
``` 
#### 采用响应式布局
```
<WebView
  originWhitelist={['*']}
  source={{ html: `
  <html>
    <head>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>

    <body>
      <p>这里是一个标题</p>
    </body>
  </html>
  ` }}
  injectedJavaScript='window.ReactNativeWebView.postMessage(document.documentElement.scrollHeight)'
  onMessage={this.onWebViewMessage}
/>
```