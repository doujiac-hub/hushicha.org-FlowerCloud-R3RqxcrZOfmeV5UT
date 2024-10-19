
## 前言：


我们已经分享过如何快速实现自己需要的全局弹框组件；


在开发 Vue 项目时，特别是H5页面的项目，还有一个组件是我们非常常用的，它相对弹框来说没有那么大，并且不需要手动关闭在需要更简洁的提示用户一些信息时非常常用，它就是 `toast` 提示组件；


接下来我们会带着大家手写一个全局的 `toast` 提示组件，当你在项目任何地方需要使用时，都可直接调用。


查看往期文章：


[十五分钟两百行代码，手写一个vue项目全局通用的弹框](https://github.com)


## 第一步：新建文件夹及主要文件


Vue项目中，一般来说我们公用组件是放在 `src/components`，所以我们直接在`src/components/toast`下新建如下两个文件：


1. index.vue：该文件是 `toast` 组件的内容，跟我们写普通 vue 组件一样，包含 `toast` 的结构、样式以及基础逻辑；
2. index.js：注册 `index.vue` 组件为全局组件。因为该组件我们不需要手动关闭，并且涉及到添加元素和自动删除元素，所以该文件会有一些元素层面上的操作和逻辑，相对上期的弹框组件的 `index.js` 的文件来说会相对复杂些，我们下边会逐行讲解，当然也会提供完整代码，请往下看。


## 第二步：书写组件内容


### index.vue组件内容如下：


1. 结构 \+ js 代码



```


|  |  |
| --- | --- |
|  | <transition name="appear"> |
|  | <section class="toast" v-if="show"> |
|  | <div v-html="msg" class="toast-con">div> |
|  | section> |
|  | transition> |
|  |  |
|  |  |
|  | <script type="text/ecmascript-6"> |
|  | export default { |
|  | name: "toast", |
|  | data() { |
|  | return { |
|  | show: false, |
|  | msg: '', |
|  | time: 1000 |
|  | } |
|  | }, |
|  | methods: { |
|  | async open() { |
|  | if (this.show) { |
|  | return; |
|  | } |
|  | this.show = true; |
|  | let result = await this.close(); |
|  | return result; |
|  | }, |
|  | close() { |
|  | return new Promise((resolve) => { |
|  | setTimeout(() => { |
|  | this.show = false; |
|  | resolve(true); |
|  | }, this.time); |
|  | }); |
|  | } |
|  | } |
|  | } |
|  | script> |
|  |  |


```

代码说明：


* 我们这里用到了 `Vue` 的 `transition` 组件，用于包裹需要动画效果的元素。name\="appear" 指定了使用名为 "appear" 的过渡效果；
	+ 详情参考 Vue 官网：[https://cn.vuejs.org/guide/built\-ins/transition.html](https://github.com):[westworld加速](https://tianchuang88.com)
* 这句代码是我们toast的主要内容，通过 `v-html + msg` 来动态属性来插入，msg在index.js 中修改;
* methods：定义组件的方法。
	+ open()：异步方法，用于显示 Toast。如果已显示，则直接返回；否则设置 show 为 true 并调用 close() 方法等待其完成。
	+ close()：返回一个 Promise，该 Promise 在 time 毫秒后解决，同时将 show 设置为 false，从而隐藏 Toast。


2. 样式



```


|  | <style lang="less" scoped> |
| --- | --- |
|  | .default-message { |
|  | position: fixed; |
|  | right: 0; |
|  | top: 0; |
|  | bottom: 0; |
|  | left: 0; |
|  | width: 100%; |
|  | height: 100%; |
|  | z-index: 1000; |
|  | background: rgba(0, 0, 0, 0.7); |
|  |  |
|  | .default-message-title { |
|  | color: #333; |
|  | margin: 0; |
|  | line-height: 1.5; |
|  | font-size: 18px; |
|  | min-height: 18px; |
|  | padding-top: 20px; |
|  | text-overflow: ellipsis; |
|  | font-weight: bold; |
|  | cursor: move; |
|  | text-align: center; |
|  | } |
|  |  |
|  | .default-message-content { |
|  | width: 85%; |
|  | position: absolute; |
|  | top: 50%; |
|  | left: 50%; |
|  | transform: translate3d(-50%, -50%, 0); |
|  | background-color: #fff; |
|  | border-radius: 6px; |
|  | transition: all 0.2s ease-in; |
|  | color: #999; |
|  | font-size: 18px; |
|  | } |
|  |  |
|  | .default-message-value { |
|  | padding: 28px 18px; |
|  | text-align: center; |
|  | position: relative; |
|  | color: #999; |
|  | text-align: center; |
|  | font-size: 14px; |
|  | color: rgba(102, 102, 102, 1); |
|  | } |
|  | .default-message-btns { |
|  | // border-top: 1px solid #ddd; |
|  | display: flex; |
|  | height: 60px; |
|  | position: relative; |
|  | &:after { |
|  | position: absolute; |
|  | content: ""; |
|  | display: inline-block; |
|  | left: 0; |
|  | right: 0; |
|  | top: 0; |
|  | height: 1px; |
|  | transform: scaleY(0.5); |
|  | background: #ddd; |
|  | } |
|  | .default-message-btn { |
|  | flex: 1; |
|  | display: flex; |
|  | align-items: center; |
|  | justify-content: center; |
|  | font-size: 16px; |
|  | padding: 0 3px; |
|  | } |
|  | .default-message-submit { |
|  | color: #26a2ff; |
|  | } |
|  | .default-message-cancle { |
|  | color: #999; |
|  | position: relative; |
|  | &:after { |
|  | position: absolute; |
|  | content: ""; |
|  | display: inline-block; |
|  | top: 0; |
|  | right: 0; |
|  | bottom: 0; |
|  | width: 1px; |
|  | transform: scaleX(0.5); |
|  | background: #ddd; |
|  | } |
|  | } |
|  | } |
|  | @keyframes fadeIn { |
|  | from { |
|  | opacity: 0; |
|  | } |
|  | to { |
|  | opacity: 1; |
|  | } |
|  | } |
|  | } |
|  | style> |


```

## 第三步：注册成全局组件



```


|  | import { createApp } from 'vue'; |
| --- | --- |
|  | import ToastComponents from './index.vue'; |
|  |  |
|  | const LayerToastId = 'layer-Toast-wrapper'; |
|  |  |
|  | let Toast = async function (msg, time) { |
|  | time = time || 2000; |
|  |  |
|  | let ToastEl = document.getElementById(LayerToastId); |
|  | // 如果DOM中含有这个元素 不执行 |
|  | if (ToastEl) { |
|  | return; |
|  | } |
|  | const div = document.createElement('div'); |
|  | div.setAttribute('id', LayerToastId) |
|  | document.body.appendChild(div); |
|  | let layerToastEl = createApp(ToastComponents).mount('#' + LayerToastId); |
|  | // 修改组件中的data的值 |
|  | layerToastEl.msg = msg; |
|  | layerToastEl.time = time; |
|  | // 执行组件中的方法 等待关闭后返回promise |
|  | let hasClosed = await layerToastEl.open(); |
|  | // 当Toast提示关闭后再删除外层元素 时间最好与css动画一致 |
|  | if (hasClosed) { |
|  | setTimeout(() => { |
|  | document.body.removeChild(div); |
|  | }, 400); |
|  | } |
|  | }; |
|  | export default { |
|  | install (app) { |
|  | // 通过this.$toast访问 |
|  | app.config.globalProperties.$toast = Toast; |
|  | } |
|  | } |
|  |  |


```

到这里，我们的弹框组件就完成了。下边我们对一些比较重要的代码做个解释：


* `let Toast = async function` 这里表示Toast消息，我们把它注册成一个异步函数，因为内部需要使用到定时器控制定时移除消息容器；
* `let layerToastEl = createApp(ToastComponents).mount('#' + LayerToastId);`


	+ 这句代码的意思是：把我们引入的 `index.vue` 文件创建成一个 Vue 的应用实例，并挂载到新创建的 div 上。
* 以下是 `index.js` 文件的逐行解释：



```


|  | // 引入 Vue 的 createApp 函数，用于创建 Vue 应用实例 |
| --- | --- |
|  | import { createApp } from 'vue'; |
|  | // 引入 Toast 组件 |
|  | import ToastComponents from './index.vue'; |
|  |  |
|  | // 定义一个常量，用于存储 Toast 组件的容器元素的 ID |
|  | const LayerToastId = 'layer-Toast-wrapper'; |
|  |  |
|  | // 定义一个异步函数 Toast，用于显示 Toast 消息 |
|  | let Toast = async function (msg, time) { |
|  | // 如果未指定显示时间，默认为 2000 毫秒 |
|  | time = time || 2000; |
|  |  |
|  | // 获取页面上是否已存在 Toast 容器元素 |
|  | let ToastEl = document.getElementById(LayerToastId); |
|  | // 如果已存在，不执行后续代码，直接返回 |
|  | if (ToastEl) { |
|  | return; |
|  | } |
|  | // 创建一个 div 元素，用作 Toast 组件的容器 |
|  | const div = document.createElement('div'); |
|  | // 为该 div 设置 ID |
|  | div.setAttribute('id', LayerToastId) |
|  | // 将创建的 div 添加到 body 中 |
|  | document.body.appendChild(div); |
|  |  |
|  | // 创建一个 Vue 应用实例，并挂载到新创建的 div 上 |
|  | let layerToastEl = createApp(ToastComponents).mount('#' + LayerToastId); |
|  | // 设置 Toast 组件的消息内容和显示时间 |
|  | layerToastEl.msg = msg; |
|  | layerToastEl.time = time; |
|  |  |
|  | // 调用 Toast 组件的 open 方法，显示 Toast 并等待其关闭 |
|  | let hasClosed = await layerToastEl.open(); |
|  | // 当 Toast 关闭后，延迟 400 毫秒后移除 Toast 容器元素 |
|  | // 这里的 400 毫秒延时可以与 CSS 动画的时间相匹配，确保动画播放完成 |
|  | if (hasClosed) { |
|  | setTimeout(() => { |
|  | document.body.removeChild(div); |
|  | }, 400); |
|  | } |
|  | }; |
|  |  |
|  | // 导出一个对象，包含 install 方法，用于在 Vue 应用中安装这个 Toast 功能 |
|  | export default { |
|  | install (app) { |
|  | // 将 Toast 函数添加到 Vue 应用的全局属性中，使其可以通过 this.$toast 在任何组件中访问 |
|  | app.config.globalProperties.$toast = Toast; |
|  | } |
|  | } |


```

## 项目中使用弹框


使用就非常简单便利了，主要有以下几种用法：


1. Vue2 中使用：



```


|  | // Vue2 中简单使用 |
| --- | --- |
|  | this.$toast("Toast提示在Vue2项目中的简单使用")); |
|  |  |
|  | // Vue2中需要在提示后有进一步操作：可以任何你想的逻辑，包括发接口、页面处理等。 |
|  | await this.$toast("Toast提示在Vue2项目中使用后有后续逻辑", 3000); |
|  | handleFunction(); // 这里的函数代表提示后的逻辑代码 |


```

2. Vue3 中使用：



```


|  | // 在 Vue3 中使用时需要先引入app |
| --- | --- |
|  | import { app } from "@/main"; |
|  |  |
|  | // Vue3 中简单使用 |
|  | app.config.globalProperties.$toast("Toast提示在Vue3项目中的简单使用", 3000); |
|  |  |
|  | // Vue2中需要在提示后有进一步操作：可以任何你想的逻辑，包括发接口、页面处理等。 |
|  | app.config.globalProperties.$toast("Toast提示在Vue3项目中使用后有后续逻辑"); |
|  | handleFunction(); // 这里的函数代表提示后的逻辑代码 |


```

说明：


* 我们可以在使用时传入合适的显示停留时间，如果为传入，则按默认的 2000 毫秒显示；
* 在 Vue3 中，你也可以把 `$toast` 重新保存一下，后续不用每次都写很长的 `app.config....`



```


|  | import { app } from "@/main"; |
| --- | --- |
|  |  |
|  | const toast = app.config.globalProperties.$toast |
|  |  |
|  | toast("简便地使用toast提示"); |


```

## toast 图片示例


![](https://img2024.cnblogs.com/blog/3004199/202410/3004199-20241018092643560-991985586.gif)


说明：在停留 2000 毫秒（或者我们设置的停留时间）之后会自动关闭。


## 写在后面


这是一个比较基础和通用的黑色半透明提示消息，这边示例的代码是比较全的，对细节要求不大的小伙伴可以直接抄作业；


背景颜色、字体、布局等这些细节，因为每个业务场景不同，大家可以根据自己的需要适当调整；


通过修改结构和样式代码，你可以让消息的样式变得更加丰富或者更符合你的业务需求；


消息组件我们一样是使用固定单位的，如果小伙伴的项目需要使用响应式大小，直接对应替换大小单位（rem、vw）即可；


对你有帮助的话给作者点点关注吧，你的支持是我不断更新的动力！Peace and love\~\~


