# vue-pop
vue气泡提示插件

## 安装
```
npm install vue-pop
```
## 使用
### 一，注册组件
```
// 这里使用在main.js中全局注册来示例
import vue from 'vue'
import pop from 'vue-pop'
vue.component('pop', popTip)
```
### 二，vue文件中使用
```
<template>
  <pop v-ref:pop>
    <div>我是一个div</div>
  </pop>
  <button @click="hi">hi</button>
  <button @click="hello">hello</button>
</template>
<script>
export default{
  created () {
    // 分别为两个按钮创建两个pop实例
    this.hiPop = this.$refs.pop.new()
    this.helloPop = this.$refs.pop.new()
  },
  methods: {
    hi () {
      this.hiPop.isShow ? this.hiPop.hide() : this.hiPop.show('hi')
    },
    hello () {
      this.helloPop.isShow ? this.helloPop.hide() : this.helloPop.show('hello')
    }
  }
}
</script>
```
## 为什么不用组件的props数据响应来控制而是要调用实例方法？
如果你看了上面的示例，可以发现有时候会有不同的地方调用同一个pop，通过为各自创建不同实例的方式可以实现实例之间互不影响：<br>
* 先点击"hi"按钮，出现"hi"气泡
* 再点击"hello"按钮，出现"hello"气泡（"hi"气泡被覆盖）
* 再点击"hello"按钮，"hello"气泡消失（"hi"气泡重新出现）

也就是说同一个pop组件，只会显示最后一次show方法设置的内容，但会按照调用顺序储存内容队列。然后各自的pop实例维护各自的show，hide

## 方法
### show (content)
显示气泡。content:气泡内容

### hide ()
关闭气泡

## 属性
### isShow
当前气泡实例是否处于显示状态。(**有可能会排在后来的显示队列前面，因此不保证可见**)

## Props
### bottom
使气泡始终出现在目标元素底部。默认情况下气泡优先会出现在顶部，除非它检测到顶部空间不足以显示自己才会在底部出现
```
<pop bottom>
  <div>我是一个div</div>
</pop>
```
### zIndex
气泡的style.zIndex值。默认：9999999
```
<pop z-index="100">
  <div>我是一个div</div>
</pop>
```
