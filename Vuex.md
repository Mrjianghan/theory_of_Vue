# 非父子组件通信的几种方式

- Vue提供了`$emit()`和`$on`来分别进行事件触发和事件监听 <br />
- 对于较小的项目，我们直接使用eventBus来进行非父子组件之间的通信 <br />
- 对于较大的项目，使用官方提供的Vuex来进行通信 <br />

# 解读$emit()和$on()

对于这两个实例方法，官网的API中这样写到： <br />

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
`$emit`的第一个参数是事件名称，是一个字符串。第二个参数是附加的参数象，如对、数字、布尔值等。<br />
`$on`的第一个参数是事件名称，也是个字符串，是用来和`$emit`中的字符串来匹配的。相同的话就是一个触发一个监听。第二个参数是回调函数，就是这个监听到这个事件了，要执行的函数。`$emit`中的第二个参数，会作为这个回调函数的参数被传入。<br />
再看下官网的例子，马上就懂了。<br />
```js
vm.$on('test', function (msg) {
  console.log(msg)
})
vm.$emit('test', 'hi')
// 输出 "hi"
```

# 父子组件通信

## 父组件向子组件传值
这个比较简单，在子组件里写props，写好要接收的参数，然后在父组件中的子组件标签里传入即可。

## 子组件向父组件传值

在子组件中写好 `$emit`，然后在父组件中的子组件标签中用v-on来监听即可，参考官网教程。

## 小项目的非父子组件之间的通信

你会百度到`eventBus`很多的博客，但貌似都写的乱七八糟的。这里就手把手教你写一个全局eventBus。<br />
这里就要用到上面讲的`$emit`和`$on`了，<br />。

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
这种触发-监听过程，可以创建n个，只要是事件名能对上就行。到这里，基本简单的项目中的通信方法，你都会了。











