<template>
  <pop-el-wrap>
    <pop-el v-show="isShow" :class="{bottom: isBottom}"
            :style="{left: left + 'px', top: top + 'px', zIndex: zIndex || 9999999}" v-el:pop>
      <pop-el-body>
        {{tip}}
      </pop-el-body>
      <pop-el-arrow></pop-el-arrow>
    </pop-el>
    <slot></slot>
  </pop-el-wrap>
</template>
<style scoped>
  pop-el-wrap {
    position: relative;
  }

  pop-el {
    position: absolute;
  }

  pop-el-body {
    padding: 5px 10px;
    background-color: rgb(254, 241, 241);
    color: #f94949;
    font-size: 13px;
    border-radius: 5px;
    box-shadow: 1px 2px 1px rgb(225, 221, 222);
    white-space: nowrap;
  }

  pop-el-arrow {
    display: inline-block;
    height: 8px;
    width: 8px;
    position: absolute;
    left: 0;
    right: 0;
    top: 19px;
    margin: auto;
    transform: rotate(45deg);
    background-color: rgb(254, 241, 241);
    box-shadow: 1px 2px 1px rgb(225, 221, 222);
  }

  pop-el.bottom pop-el-body {
    box-shadow: 1px -2px 1px rgb(225, 221, 222);
  }

  pop-el.bottom pop-el-arrow {
    top: -5px;
    transform: rotate(225deg);
  }

</style>
<script>
  // 手动注册自定义标签来消灭Unknown custom element错误警告
  for (let tagName of ['pop-el-wrap', 'pop-el', 'pop-el-body', 'pop-el-arrow']) {
    try {
      document.registerElement(tagName)
    } catch (registerErr) {
    }
  }

  function getOffset (n1, n2) {
    const nr1 = n1.getBoundingClientRect()
    const nr2 = n2.getBoundingClientRect()
    return {
      left: nr1.left - nr2.left,
      top: nr1.top - nr2.top
    }
  }

  export default{
    data () {
      return {
        isBottom: false,
        left: 0,
        top: 0,
        index: 0,
        record: [],
        tips: []
      }
    },
    props: ['bottom', 'zIndex'],
    computed: {
      tip () {
        return this.tips[this.tips.length - 1]
      },
      isShow () {
        return this.record.length > 0
      }
    },
    watch: {
      isShow (is) {
        if (is) {
          const wrap = this.$el
          const tRect = this.$els.pop.getBoundingClientRect()
          const tipHeight = tRect.height + 10
          const tipWidth = tRect.width
          const sRect = this.getSlotRect()
          const offset = getOffset(this.getSlot(), wrap)
          this.isBottom = this.bottom !== undefined || sRect.top < tipHeight
          this.top = this.isBottom ? offset.top + sRect.height + 10 : offset.top - tipHeight
          this.left = (offset.left + sRect.width / 2) - tipWidth / 2
        }
      }
    },
    ready () {
      if (window.getComputedStyle(this.getSlot()).display === 'block') this.$el.style.display = 'block'
    },
    methods: {
      getSlot () {
        return this.slot || (this.slot = this.$el.lastElementChild)
      },
      getSlotRect () {
        return this.getSlot().getBoundingClientRect()
      },
      new () {
        const self = this
        const index = this.index++
        return {
          isShow: false,
          show (tip) {
            if (typeof tip === 'string') {
              if (tip.length === 0) return this.hide()
              const sRect = self.getSlotRect()
              if (sRect.height === 0 && sRect.width === 0) {
                console.log(self)
                return console.warn('tip can not show on an hidden popTip')
              }
              let aIndex = self.record.indexOf(index)
              aIndex = (aIndex === -1 ? self.record.length : aIndex)
              self.tips.$set(aIndex, tip)
              self.$nextTick(() => self.record.$set(aIndex, index))
              this.isShow = true
            }
          },
          hide () {
            const aIndex = self.record.indexOf(index)
            if (aIndex !== -1) {
              self.record.splice(aIndex, 1)
              self.tips.splice(aIndex, 1)
              this.isShow = false
            }
          }
        }
      }
    }
  }
</script>
