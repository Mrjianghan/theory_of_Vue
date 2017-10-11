# 解读$emit()和$on()

理解了这两个方法，简单的组件通信就会了一大半。关于它们，官网的API中这样写到： <br />

#### vm.$emit( event, […args] )
```js
  // 参数：
  {string} event
  [...args]
  // 触发当前实例上的事件。附加参数都会传给监听器回调。
```

#### vm.$on( event, callback )
```js
  // 参数：
  {string | Array<string>} event // (数组只在 2.2.0+ 中支持)
  {Function} callback
  // 监听当前实例上的自定义事件。事件可以由vm.$emit触发。回调函数会接收所有传入事件触发函数的额外参数。
```
看了这么简练的描述，是不是有种抓不住重点的感觉？往下看：<br />

- `$emit()` 和 `$on()`是实例方法，那么它们只能对一个实例有用。<br />
在一个实例中被`$emit`触发的事件，只能在相同的实例中被`$on`监听。<br />
举个例子对比一下：官网上在`组件-自定义事件`这部分中，讲述了如何进行子组件向父组件通信的方法，里面有句：<br />
`不能用 $on 侦听子组件释放的事件`,<br />
其实本质就是，子组件`$emit`的事件，父组件用`$on`是监听不到的。<br />

- 参数说明 <br />
`$emit`的第一个参数是事件名称，是一个字符串。第二个参数是附加的参数象，如对象、数字、布尔值等。<br />
`$on`的第一个参数是事件名称，也是个字符串，是用来和`$emit`中的字符串来匹配的。相同的话就是一个触发一个监听。第二个参数是回调函数，就是这个监听到这个事件了，要执行的函数。`$emit`中的第二个参数，会作为这个回调函数的参数被传入。<br />
再看下官网的例子，马上就懂了。<br />
```js
vm.$on('test', function (msg) {
  console.log(msg)
})
vm.$emit('test', 'hi')
// 输出 "hi"
```

# 组件通信

## 父组件向子组件传值
这个比较简单，在子组件里写props，写好要接收的参数，然后在父组件中的子组件标签里传入即可。

## 子组件向父组件传值

在子组件中写好 `$emit`，然后在父组件中的子组件标签中用v-on来监听即可，参考官网教程。

## 小项目里的非父子组件之间的通信

你会百度到`eventBus`很多的博客，但貌似都写的乱七八糟的。这里就手把手教你写一个全局eventBus。<br />
这里就要用到上面讲的`$emit`和`$on`了。<br />

- 原理 <br />
假如我们有一个组件A,需要在A被点击的时候，把一个number传给组件B。这时候我们可以创建一个空的vue对象，在组件A中，当A被点击的时候，调用这个空对象的`$emit`方法，把number作为`$emit`的第二个参数；然后在组件B中，我们在组件生命created钩子函数上挂上这个空对象的`$on`方法，那么我们在`$on`的回调函数中，就可以获取number了。<br />

代码如下：<br />
首先在根组件的实例中（也就是在main.js中实例化的那个vue对象）添加一个空的vue对象：<br />
```js
new Vue({
  data () {
    return {
      Bus: new Vue()
    }
  }
})
```
然后在组件A中：<br />
```vue
<template>
  <div @click="clicked"></div>
</template>
<script>
  export default {
    data () {
      return {
        // 我们要传送给组件B的值
        number: 100
      }
    }
    methods: {
      clicked () {
        // $root：根组件
        this.$root.Bus.$emit('sendMssage', this.number)
      }
    }
  }
</script>
```
这里我们用了$root属性，它是当前组件树的根组件。也就是说，如果不是在相同的根组件下，就不能用这个方法咯。（多页应用无效。毕竟调用不到Bus这个vue对象了。）<br />

最后在组件B中：<br />
```vue
<script>
  export default {
    data () {
      return {
        temp: 0
      }
    }
    created: {
      this.$root.Bus.$on('sendMessage',value => {
        this.temp = value
        console.log(this.temp)
        // 输出 100
      })
    }
  }
</script>
```
注意，这里的回调函数要写成箭头函数，防止this错乱。不想写的话，可以用that = this的老办法。<br />
这种触发-监听过程，可以创建n个，只要是事件名能对上就行。到这里，基本简单的项目中的通信方法，你都会了。<br />

# Vuex基础

#### Vuex能做什么

如果你有两个组件，分别为A和B，而你需要在A被点击的时候传递一个值number给B，那么一个简简单单的eventBus就能满足你的要求。 <br />
<br />
but，假如你有20个组件，分别为C1、C2、C3......C20，而你需要在点击C1的时候，传递一个对象obj给C2~C20。并且，这个obj有许多个属性，假如有obj.number(一个数值)、obj.add（一个方法）、obj.array(一个数组)....等等，C2~C20要分别获取这些属性，并且。。当C2获得obj.number之后，又会触发一个methods，获得一个结果answer,这个answer需要传递给C3、C4,并且传给C4之前需要把answer加上10。<br />
假如你全用eventBus来做的话，代码耦合得不能看了。。<br />
......<br />
讲了这么多，Vuex就是来处理这种很实际很复杂的问题的。比如购物车组件。比如登陆框等等。


#### 插件安装

```
npm install vuex --save
```
或者在package.json的依赖中添加vuex，再`npm install`就可以了。

#### 插件注册

在main.js中：<br />
```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
```

## 一个项目中，和Vuex相关的部分

先仔细的阅读下官方文档，如果遇到了迷惑，需要一些点拨了，这里能给你很好的解答。<br />

- 根组件中： <br />

导入Vuex、挂载store

- store文件夹中： <br />

index.js(含state、getter)、mutation.js、action.js、module文件夹（module中：子store，重要部分的通信）

- 组件中： <br />

集中处理：mapState/mapGetter/mapMutation/mapAction；<br />
单独处理：store.state.xxx 等。<br />

## 要点打通

#### mapState是做什么的

如果在组件的computed中，我们获取state的方式如下：

```js
template: `<div>{{count}}</div>`,
computed: {
  getCount () {
    return store.state.count
  }
}
```
假如我们需要调用多个state（如count1~count5），就需要命名多个计算属性。一大串的重复代码实在是浪费时间。所以Vue官方提供了简化的方法：<br />

```js
import {mapState} from Vuex
//...
computed: mapState({
  count1: count1, //或者直接写 count 官网提供了三种写法。
  count2,
  count3,
  count4,
  count5
})
```
整个世界都清净了有木有。<br />

那么问题来了，要是我这个组件的computed属性里面还有别的属性怎么办？可以用对象展开运算符：
```js
computed: {
  otherCalculation () {
    //...
  },
  ...mapState({
    //...
  })
}
```
这个操作也就是将mapState里面的方法都取出来，然后依次添加在computed里面。相当于从对象里面剥离出来了属性。<br />

同样的，mapGetter等，都是这种功能。不再赘述。 <br />

#### action是做什么的

- 因为mutation无法处理异步操作，所以就有了action这个强大的东西。<br />

- action配合promise或者async/await更为强大。


#### 其它。。应该没难的地方了吧。。感觉就是关于map的地方被卡了一下。要是有问题的话可以随时让我更新解答。



