# Vue-Vue-Router-Vuex-SSR

1. Vue+Webpack工程流搭建
2. Vue+Vue-Router+Vuex项目架构

## 服务端渲染

现在的前端框架是纯客户端渲染的，（请求🤴网站的时候，返回的html是没有什么内容的），存在问题是没有办法seo, 白屏时间较长。需要等待js加载完成，执行完成之后才会显示内容。

服务端渲染解决这些问题。

webpack升级注意 ⚠️ ：1. 版本变化 2. 配置变化 3. 插件变化

## vue-loader配置

```js
const isDev = process.env.NODE_ENV === 'development'
```

```js
// vue-loader.config.js
const docsLoader = require.resolve('./doc-loader')
module.exports = (isDev) => {
 return {
  preserveWhitespace: true, // 去掉后面的空格
  extractCSS: !isDev, // 单独打包到css某文件
  cssModules: {},
  // hotReload: false, // 根据环境变量生成
  loaders: {
   'docs': docsLoader,
  },
  preLoader: {
  },
 }
}
```

```js
module.exports = (isDev) => {
 return {
  preserveWhitespace: true,
  extractCSS: !isDev,
  cssModules: {},
 }
}
```

## css module配置

```js
module.exports = (isDev) => {
 return {
  preserveWhitespace: true,
  extractCSS: !isDev,
  cssModules: {
   localIdentName: isDev ? '[path]-[name]-[hash:base64:5]' : '[hash:base64:5]',
   // localIdentName: '[path]-[name]-[hash:base64:5]',
   camelCase: true
  }
 }
}
```

## 安装使用eslint和editorconfig以及precommit

```js
npm i eslint eslint-config-standard eslint-plugin-standard eslint-plugin-promise eslint-plugin-import eslint-plugin-node
```

创建`.eslintrc`

```js
{
 "extends": "standard"
}
```

```js
npm i eslint-plugin-html
```

```js
// package.json

"script": {
 "clean": "rimraf dist",
 "lint": "eslint --ext .js --ext .jsx --ext .vue client/",
 "build": "npm run clean && npm run build:client",
 //自动修复
 "lint-fix": "eslint --fix --ext .js --ext .jsx --ext .vue client/"
 "precommit": "npm run lint-fix",
}
```

```js
npm i webpack -D
// webpack 4
npm uninstall webpack webpack-dev-server webpack-merge -D
// webpack 升级
npm i webpack webpack-dev-server webpack-merge webpack-cli -D

npm uninstall babel-loader extract-text-webpack-plugin file-loader html-webpack-plugin -D
```

## webpack.config.base.js

```js
const config = {
 mode: process.env.NODE_ENV, // development || production
 target: 'web',
}
```

```js
npm i eslint-loader babel-eslint
```

## Vue的一些点

```js
import Vue from 'vue'

const div = document.createElement('div')
document.body.appendChild(div)

new Vue({
 el: div,
 template: '<div>this is content</div>'
})

// webpack.config.practice.js
resolve: {
 alias: {
  'vue': path.join(__dirname, '../node_modules/vue/dist/vue.esm.js')
 }
}
```

index.js

```js
import Vue from 'vue'
import App from './app.vue'

import './assets/styles/global.style'

const root = document.createElement('div')
document.body.appendChild(root)

new Vue({
 rendeer: (h) => h(App)
}).$mount(root)
```

## VUE实例

- Vue实例的创建和作用
- Vue实例的属性
- Vue实例的方法

```js
import Vue from 'vue'
const app = new Vue({
 // el: '#root',
 template: '<div ref="div">{{text}}</div>',
 data: {
  text: 0
 }
})

app.$mount('#root')

setInterval(()=>{
 // app.text += 1
 // app.$options.data += 1 不可以使用
 app.$data.text += 1 // 可以使用
},1000)

console.log(app.$data)
console.log(app.$props)
console.log(app.$el)
console.log(app.$options)

console.log(app.$slots)
console.log(app.$scopedSlots)

console.log(app.$ref) // div节点/组件实例

console.log(app.$isServer) // 服务端渲染

app.$options.render = (h) => {
 return h('div', {}, 'new render function')
}  
```

```js
props: {
 filter: {
  type: String,
  required: true
 },
 todos: {
  type: Array,
  required: true
 }
}
```

## watch

```js
const app = new Vue({
 // el: '#root',
 template: '<div ref="div">{{text}}</div>',
 data: {
  text: 0
 },
 watch: {
  text(newText, oldText) {
   console.log('${newText}:${oldText}')
  }
 }
})
app.$mount('#root')
```

```js
const unWatch = app.$watch('text', (newText, oldText){
 console.log(`${nextText}:${oldText}`)
})
setTimeout(()=>{
 unWatch() // 取消 2秒后
}, 2000)
```

事件监听：

```js
app.$on('test', () => {
 console.log('test emited')
})

//触发事件
app.$emit('test')

app.$on('test', (a, b) => {
 console.log('test emited ${a} ${b}')
})

//触发事件
app.$emit('test', 1, 2)
```

```js
$once只监听一次
app.$once('test', (a, b) => {
 console.log('test emited ${a} ${b}')
})
```

## forceUpdate强制组件渲染一次

非响应式的：

```js
// 值在变，但不会导致重新渲染
data: {
 text: 0,
 obj: {}
}

setInterval(() => {
 app.obj.a = app.text
 app.$forceUpdate()
},1000)
```

一直在渲染会让你的性能降低

某个对象上的，某个属性名，给他定义一个值：

```js
let i = 0
setInterval(()=>{
 i++
 app.$set(app.obj, 'a', i) // 变化，某个对象上的某个属性值给它一个值
},1000)
```

## vm.$nextTick([callback])

等DOM节点渲染完成。Vue在下一次更新的时候调用callback

将回调延迟到下次DOM更新循环之后执行，在修改数据之后立即使用它，然后等待DOM更新，它跟全局方法Vue.nextTick一样，不同的是回调的this自动绑定到调用它的实例上。

2.1.0新增，如果没有提供回调且支持Promise的环境中，则返回一个Promise.注意polyfill.

## vue生命周期

```js
new Vue({
 el: '#root',
 template: '<div>{{text}}</div>',
 data: {
  text: 'jeskson'
 },
 beforeCreate() {
  console.log(this, 'beforeCreate')
 },
 created() {
  console.log(this, 'created')
 },
 beforeMount() {
  console.log(this, 'beforeMount')
 },
 mounted() {
  console.log(this, 'beforeCreate')
 },
 beforeUpdate() {
  console.log(this, 'beforeUpdate')
 },
 updated() {
  console.log(this, 'updated')
 },
 activated() {
  console.log(this, 'activated')
 },
 deactivated() {
  console.log(this, 'deactivated')
 },
 beforeDestroy() {
  console.log(this, 'beforeDestroy')
 },
 destroyed() {
  console.log(this, 'destroyed')
 },
 render(h) {
  console.log('render function invoked')
  return h('div', {}, this.text)
 },
 renderError(h, err) {
  return h('div', {}, err.stack)
 },
 errorCaptured() {
  // 向上冒泡，并且正式环境可以使用
 }
})
```

```js
undefined 'beforeCreate'
undefined 'created'
<div id="root"></div> "beforeMount"
<div>0</div> "mounted"

template: `<div> name: {{name}} </div>`

computed: {
 name() {
  return `${this.firstName} ${this.lastName}`
 } 
}

<li v-for="(item, index) in arr">遍历数组</li>
<li v-for="(val, key) in obj"></li>

v-model.number 数字
v-model.trim 去掉空格
<div>
 <input type="radio" value="one" v-model="pick"/>
 <input type="radio" value="two" v-model="pick"/>
</div>
```

new Vue, beforeCreate, created, beforeMount, mounted

没有el，就没有挂载beforeMount, mounted

若使用const app , app.$mount('#root') 就会执行

```js
setInterval(() => {
 app.text = app.text += 1
},1000)

// 主动销毁
app.$destroy()
```

组件：

```js
activated() {
 // 在组件
 console.log(this, 'activated')
}
deactivated() {
 // 在组件
 console.log(this, 'deactivated')
}
```

变化：

el:

```js
undefined 'beforeCreate'
undefined 'created'
<div id="root"></div> "beforeMount"
<div>0</div> "mounted"
```

dom有关的放在mounted里面，数据可created,mounted

服务端渲染不会执行（因为服务端没有Dom环境，所有更本就没有这些内容）,beforeMount，mounted，
会执行create，beforeCreate

## 生命周期

new Vue()

Init Events(事件已经ok了) & Lifecycle

beforeCreate(不要修改data里的数据)

Init injections & reactivity

created(数据操作)

Has 'el' option ? No when vm.$mount(el) is called

- 判断是否有el // el: '#root'

YES Has 'template' option ?

- 判断是否有 // template: ' `<div>` {{text}} `</div>` '

有template属性：

解析render

```js
render(h) {
 console.log('render function invoked')
 return h('div', {}, this.text)
}

Waiting for update signal form WDS...

undefined "beforeCreate"
undefined "created"
 <div id="root"></div> "beforeMount"
render function invoked
 <div>0</div> "mounted"
```

Yes:有 Compile template into render function

No:没有 Compile el's  outerHTML as template

beforeMount

Create vm.$el and replace 'el' with it

mounted

Mounted(实例创建完成)

- when data changes (beforeUpdate) Virtual DOM re-render and patch (updated)
- 走 when wm.$destroy() is called

beforeDestroy

Teardown watchers, child components and event listeners

destroyed

> renderError

打包正式上线不会调用的，开发的时候会使用

```js
renderError (h, err) {
 return h('div', {}, err.stack)
}
```

正式开发环境，收集线上的错误

```js
errorCaptured() {
 <!-- 会向上冒泡 -->
}
```

vue里面的data绑定到template

watch监听到某个一数据的变化，指定某个操作，（服务器使用）

Vue的原生指令
