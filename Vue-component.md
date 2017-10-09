# 组件是什么
举个例子：组件就像个手机，你不需要知道芯片、电池、屏幕这些东西是如何运作的，你只需要在它的屏幕上（接口）点几下（输入），就能打电话发消息玩游戏（实现）。<br />
所以在写一个组件的时候，我们应该想下：<br />
- 我要这个组件实现什么样的功能？<br />
- 实现这个功能最少需要传入什么数据？<br />

# 一个完整的组件

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
  expotr default {
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
## 组件注册
写好了一个组件.vue文件之后，我们需要对它进行注册。就像我们在家里做了很多饼，然后现在要拿去卖了，至于谁买，我们不得而知，但是摆摊子这个过程，就是注册的意义所在。<br />

举个例子，假如我们已经写好了一个非常简单的组件，我们将它命名为 demo.vue,那么在demo.vue的同级目录下，我们可以新建一个index.js文件：

```js
// 先导入这个vue文件
import demoComponent from './demo.vue'
// 然后开始注册
const demo = {
  install: function (Vue) {

  }
}

```











