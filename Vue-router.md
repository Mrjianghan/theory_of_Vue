# Vue-router是什么
Vue-router是Vue官方提供的一个插件。是一个插件。<br />
Vue-router用于映射组件，并将它们渲染在指定的位置。(假如有一个盒子，这个盒子只能装一个球。你从一堆球中挑中了一个球，Vue-router做的就是将这个球装到盒子里。~~大雾~~)<br />

# 打通任督二脉的教程

## 安装Vue-router

如果是采用webpack模板的话，里面已经集成了Vue-router。<br />
如果没有，或者用 npm install vue-router 命令，或者在package.json的依赖中添加完vue-router之后再npm install<br />

## 注册Vue-router
##### ps:官网上的教程是从HTML部分到JS部分，不过为了便于理解，我们先从JS部分开始
在这里建议大家把所有的路由，都写在一个js文件内，方便管理。例如在src下新建一个名为router.js的文件。<br />
在使用Vue-router之前，我们要注册它：<br />
```js
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)
```
实际上，这和注册组件没什么不同。<br />

## 组件映射
然后我们导入需要映射的组件：<br />
```js
import componentA from 'componentA_url' // url是componentA.vue文件的路径
import componentB from 'componentB_url'
// 定义routes数组
const routes = [
  {path: '/a', component: componentA},
  {path: '/b', component: componentB}
  // 这里的path,比如浏览器现在的地址是https://cn.vuejs.org，当渲染了componentA之后，页面地址变为https://cn.vuejs.org/a;如果渲染了componentB,则为https://cn.vuejs.org/b
]
```
## 创建Router实例并赋值
```js
const router = new Router({
  routes: routes
  // 将定义的routes数组赋值给新对象router的属性routes
})
// 最后导出这个路由对象
export default router
```
到这里，我们的路由就写完了。<br />

## 将路由挂载到根实例上
我们需要把写完的路由挂载到根实例上，也就是导入到main.js中的那个Vue实例中。<br />
在main.js中：<br />
```js
import router from './router.js'
new Vue({
  el: '#app',
  router, // 也就是 router: router,这一步就是给根实例的router属性赋值。
  template: '<app>'
  // 等等...
})
```
这下我们彻底的写完了router的js逻辑层。接下来是写html相关的：<br />

## 路由标签
写了这么多，那么路由在html上到底是什么样的？实际上它的表现和一个<a>标签差不多（默认情况下路由标签被解析成一个<a>标签）<br />
我们在写完以上的代码之后，就可以在任意的vue文件的<template></template>块中使用映射好的路由了。<br />
比如我们在一个叫demo.vue的文件中：
```html
<template>
  <div class="demo">
    <div class="switch-bar">
      <router-link to='/a'>点我切换到组件A</router-link>
      <router-link to='/b'>点我切换到组件B</router-link>
    </div>
    <router-view></router-view>
  </div>
</template>
```
#### 怎么理解
- <router-link>  <br />
详情见vue-router官网，写的很详细了。这里讲一点就是，<router-link>默认解析成<a>标签的效果。它本身已经绑定了点击事件，还有其他的一些东西。也就是说，当你点击这个元素的时候，就会跳转到to绑定的path上，而这个path在路由对象中，正是对应着某个组件。然后这个组件，会在这个HTML中的<router-view></router-view>标签中被渲染。

- <router-view>  <br />
这是组件渲染的地方。你可以把它当做一个占位置的元素。当组件被渲染的时候，它就占据了<router-view>的位置。<br />

## 其它
到这里组件的基本知识就搞定了，接下来会出现几个问题： <br />

- 我希望在往router中的组件里传值，该怎么做？ <br />
先在组件内写好props<br />
```js
props: {
  dataINeed : {
    type: Number
  }
}
```
然后：<br />

```html
<router-view :dataINeed="data"></router-view>
//这里的data由同一个组件下的<script>部分内定义：
<script>
  data () {
    return {
      data: 0
    }
  }
</script>
```
- 我希望用户刚进入网站时候就显示这个页面  <br />
请先阅读 vue-router 重定向部分<br />
我们在router.js中做如下添加：
```js
const routes = [
  // 重定向，页面一打开就会加载componentA
  {path: '*', redirect: '/a'}

  {path: '/a', component: componentA},
  {path: '/b', component: componentB}
]
```
- 组件来回切换的时候，能不能保持一下状态啊？ <br />
如下：<br />
```html
<keep-alive>
  <router-view></router-view>
</keep-alive>
```
用<keep-alive>标签来缓存组件，但是需要注意，如果有些状态无法保留，比如窗口滚动的位置，你可以在组件中，将这个滚动的位置存入到data中，当组件在<keep-alive>内被切换回来的时候，会触发 activated 和 deactivated 钩子函数，我们可以利用这个钩子函数，重新定位窗口滚动位置。

```js
export default {
  data () {
    return {
      // 在<template>中绑定窗口的监听事件，并将scrollTop的值赋值给scrollPosition，详细代码就不写了。
      scrollPosition: 0
    }
  },
  activated () {
    // 在这个钩子函数内给窗口高度赋值为scrollPosition
  }
}
```






