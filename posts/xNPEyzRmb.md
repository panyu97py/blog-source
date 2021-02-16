---
title: 'vue -- 事件总线 EventBus'
date: 2021-02-17 01:01:03
tags: [vue]
published: true
hideInList: false
feature: 
isTop: false
---
本文转自[segmentfault](https://segmentfault.com/a/1190000021707081)

本文仅记录存在这种事件传递的方式，但不推崇。

`EventBus`又称为事件总线。在`Vue`中可以使用 `EventBus` 来作为组件传递数据的桥梁的，就像是所有组件共用相同的事件中心，可以向该中心注册发送事件或接收事件，所以组件都可以上下平行地通知其他组件，但也就是太方便所以若使用不慎，就会造成难以维护的灾难，因此才需要更完善的`Vuex`作为状态管理中心，将通知的概念上升到共享状态层次。

>官方推荐的状态管理方案是 `Vuex`。不过如果项目不是很大，状态管理也没有很复杂的话，使用 Vuex 没必要。

## 使用 EventBus
> 你需要做的只是引入 `Vue` 并导出它的一个实例（在这种情况下，我称它为 `EventBus` ）。实质上它是一个不具备 `DOM` 的组件，它具有的仅仅只是它实例方法而已，因此它非常的轻便。
### 初始化

* 第一种方式: 可以在 `main.js`中，初始化 `EventBus`

```
Vue.prototype.$EventBus = new Vue()
```

* 第二种方式: 创建一个 `Bus.js`,再创建事件总线并将其导出，以便其它模块可以使用或者监听它

```
// Bus.js
import Vue from 'vue'
export const EventBus = new Vue();
```

现在我们已经创建了 `EventBus` ，接下来你需要做到的就是在你的组件中加载它，并且调用同一个方法，就如你在父子组件中互相传递消息一样。

### 发送和接受事件

其实和 父子组件的通信差不多,用 `EventBus.$emit('eventName',data)`发送， `EventBus.$on("eventName", callback(payload1,…))` 接受

现在假设 A 组件与 B 组件通信

```
<!-- A.vue -->
<template>
    <p>{{msgB}}</p>
    <button @click="sendMsgA()">-</button>
</template>

<script> 
import { EventBus } from "../Bus.js";
export default {
    data(){
        return {
        msg: ''
        }
    },
    mounted() {
        EventBus.$on("bMsg", (msg) => {
            // a组件接受 b发送来的消息
            this.msg = msg;
        });
    },
    methods: {
        sendMsgA() {
            EventBus.$emit("aMsg", '来自A页面的消息'); // a 发送数据
        }
    }
}; 
</script>


<!-- B.vue -->
<template>
  <p>{{msgA}}</p>
    <button @click="sendMsgB()">-</button>
</template>

<script> 
import { EventBus } from "../event-bus.js";
export default {
    data(){
        return {
        msg: ''
        }
    },
    mounted() {
        EventBus.$on("aMsg", (msg) => {
            // b组件接受 a发送来的消息
            this.msg = msg;
        });
    },
    methods: {
        sendMsgB() {
            EventBus.$emit("bMsg", '来自b页面的消息'); // b发送数据
        }
    }
};
</script>
```
如果只监听(接受)一次数据可以使用 `EventBus.$once('eventName', callback(payload1,…)`

### 移除移除事件监听者

* 移除单个事件监听 `EventBus.$off('eventName', callback)`
* 移除全部事件监听 `EventBus.$off()`

```
// 导入我们刚刚创建的 EventBus
import { EventBus } from '../Bus.js'

// 事件监听函数
const clickHandler = function(clickCount) {
  console.log(`Oh, hello)`)
}

// 开始监听事件
EventBus.$on('i-got-clicked', clickHandler);

// 停止监听
EventBus.$off('i-got-clicked', clickHandler);

```

## 全局`EventBus`

全局`EventBus`，虽然在某些示例中不提倡使用，但它是一种非常漂亮且简单的方法，可以跨组件之间共享数据。

它的工作原理是发布/订阅方法，通常称为 `Pub`/`Sub` 。

由于是全局的，必然所有事件都订阅它, 所有组件也发布到它，订阅组件获得更新。也就是说所有组件都能够将事件发布到总线，然后总线由另一个组件订阅，然后订阅它的组件将得到更新。

### 创建全局`EventBus`

全局事件总线只不过是一个简单的` vue` 组件。

```
var EventBus = new Vue();

Object.defineProperties(Vue.prototype, {
  $bus: {
    get: function () {
      return EventBus
    }
  }
})
// 这个初始化的第一种方法，个人感觉区别不大
```

### 使用 `$on`和`$emit`

在这个特定的总线中使用两个方法。一个用于创建发出的事件，它就是`$emit`；另一个用于订阅`$emit`,并接收参数执行回调，它就是`$on`：

```
this.$bus.$emit('nameOfEvent',{ ... pass some event data ...});

this.$bus.$on('nameOfEvent',($event) => {
    // ...
})
```

## EventBus的优缺点

### 缺点
* 大家都知道vue是单页应用，如果你在某一个页面刷新了之后，与之相关的EventBus会被移除，这样就导致业务走不下去。
* 如果业务有反复操作的页面，EventBus在监听的时候就会触发很多次，也是一个非常大的隐患。这时候我们就需要好好处理EventBus在项目中的关系。通常会用到，在vue页面销毁时，同时移除EventBus事件监听。
* 由于是都使用一个Vue实例，所以容易出现重复触发的情景，两个页面都定义了同一个事件名，并且没有用$off销毁（常出现在路由切换时）。

### 优点
* 解决了多层组件之间繁琐的事件传播。
* 使用原理十分简单，代码量少。