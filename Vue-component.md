# 组件是什么
举个例子：组件就像个手机，你不需要知道芯片、电池、屏幕这些东西是如何运作的，你只需要在它的屏幕上（接口）点几下（输入），就能打电话发消息玩游戏（实现）。<br />
所以在写一个组件的时候，我们应该想下：<br />
- 我要这个组件实现什么样的功能？<br />
- 实现这个功能最少需要传入什么数据？<br />

# 打通任督二脉的教程

## 组件主体
也就是 .vue 文件。举个例子：<br />
```vue
<tempalte>
  <div>
    <span>我是组件HTML部分</span>
  </div>
</template>

<script>
  //如果需要导入一些框架的话 比如jQuery
  import some_frame from frame_url
  //组件脚本部分
  export default {
    data () {
      a : 我是数据
    },
    props: {
      // 数据传入
    }
    created: {
      // 钩子
    },
    computed: {
      // 计算属性
    },
    watch: {
      // 监听
    },
    method: {
      // 方法
    }
    /*
      等等...
      就像搭积木一样，需要用到什么功能块，就写什么功能块。
     */
  }
</script>

<style>
  // 我是CSS部分
</style>
```
## 组件打包
写好了一个组件.vue文件之后，我们需要对它进行打包。就像我们在家里做了很多饼干，然后现在要拿去卖了，至于谁买，我们不得而知，但是把饼干装袋子这个过程，就是打包的意义所在。<br />

举个例子，假如我们已经写好了一个非常简单的组件，我们将它命名为 demo.vue,那么在demo.vue的同级目录下，我们可以新建一个index.js文件：

```js
// 先导入这个vue文件
import demoComponent from './demo.vue'
// 然后开始注册
const demo = {
  install: function (Vue) {
    Vue.component('demo', demoComponent)
  }
}
// 最后提供Vue.use调用的接口
export default demo
```
#### 我们来理解一下其中的重点：

- Vue.component <br />
首先看下vue官方文档中 vue-组件 部分，理解Vue.component('tagName', option)是用来注册全局组件的，注册之后，在同一个vue文件中的任意地方都可以调用这个组件，相当于js中的全局变量。（组件不一定是一个vue文件，也可能是一段代码）<br />

- import & export <br />
ES6语法，详情参考阮一峰老师的ES6入门；<br />
export用于规定本模块对外的接口。<br />
```js
export 某接口
```
那么什么是接口？以下是我个人理解： <br />
首先接口是一个引用数据类型（对象、函数、类）,不能是基本值类型（字符串、布尔值等）。<br />
所以会有上面的写法：<br />
```js
const demo = {
  install : someValue
}
//对象字面量，这段代码写法等同于:
var demo = new Object()
demo.install = some_frameeValue
//所以我们在这段代码中new了一个新对象demo
```
接着看里面：<br />
```js
install: function (Vue) {
  //...
}
```
这里要讲到Vue.use()这个方法了。<br />
Vue.use(plugin)用于导入全局组件，具体的功能是：<br />
- 获得export导出的全局接口plugin（暂且这么认为）<br />
所以说，我们要写这个接口，以供Vue.use()注册<br />
- 检测这个接口中是否有install方法<br />
所以说，我们要写这个install方法而不是别的。<br />
- 如果有install方法，则立即执行install方法，并传入Vue对象作为第一个参数<br />
所以说，我们要把function的第一个参数写成Vue，如果你写成function(a, b) {}，而正好你的function里面需要用到参数a,那么很不幸，这个a无论如何是传不进来的，因为Vue.use()默认install方法的第一个参数就是Vue。正确的写法是：function(Vue, a, b) {}<br />

## 组件注册
写完了组件，也打包完啦，那么有人想要买它，怎么买呢？<br />
官方提供了Vue.use()方法来注册组件/插件。<br />
假如你已经按照上面的步骤写完了demo.vue和index.js，那么接下来这样写：<br />
- 全局组件注册方式：<br />
在main.js中写下面代码：
```js
import demo from './component/demo/index.js' //这里写demo相对于main.js的路径
Vue.use(demo)
```
到这里一个全局组件就注册完了。<br />
局部组件注册方式就自己学去。<br />

## 使用
然后，我们就可以开心的在其它的页面、组件中这么用：<br />
```vue
<template>
  <demo></demo>
</template>
```
如果需要传值的话，需要先在demo组件内部写好props,例如：
```vue
<script>
  export default{
    porps: {
      myData : {
        type : Object //检测值类型
      }
    }
  }
</script>
```
然后再调用：
```vue
<template>
  <demo :my-data="someValue"></demo>
  //注意这里要把驼峰格式转化成'-'，HTML不识别大小写的。
</template>
```
## 结束
如果对你有帮助的话，我小小的求个star！










