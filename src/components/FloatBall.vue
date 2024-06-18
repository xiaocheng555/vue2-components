<template>
  <div
    ref="floatBall"
    class="v-float-ball"
    :style="getStyle">
    <slot>
      <span class="v-float-ball-placeholder"></span>
    </slot>
  </div>
</template>

<script>
export default {
  name: 'float-ball',
  props: {
    right: {
      type: String,
      default: '20px'
    },
    bottom: {
      type: String,
      default: '100px'
    },
    left: {
      type: String
    },
    top: {
      type: String
    },
    size: {
      type: Number
    },
    // 吸附位置
    stickyPlacment: {
      type: String,
      default: 'right'
    },
    // 吸附距离
    stickyDistance: {
      type: Number
    }
  },
  data () {
    return {
      posX: undefined,
      posY: undefined,
      anim: false
    }
  },
  computed: {
    getStyle () {
      let pos
      if (typeof this.posX === 'undefined') {
        pos = {
          left: this.left,
          top: this.top,
          right: this.right,
          bottom: this.bottom
        }
      } else {
        pos = {
          left: this.posX + 'px',
          top: this.posY + 'px'
        }
      }

      return {
        ...pos,
        width: this.size + 'px',
        height: this.size + 'px',
        transition: this.anim ? '' : 'none'
      }
    }
  },
  methods: {
    // 初始化
    init () {
      this.$el.addEventListener('mousedown', this.startDrag)
    },
    // 开始拖动
    startDrag (e) {
      // 鼠标点击的点相对于拖动元素的位置
      this.disX = e.clientX - this.$el.offsetLeft
      this.disY = e.clientY - this.$el.offsetTop

      this.isClick = true
      document.addEventListener('mousemove', this.draging)
      document.addEventListener('mouseup', this.stopDrag)
      e.preventDefault()
    },
    // 正在拖动
    draging (e) {
      this.isClick = false // 非点击事件
      this.anim = false // 禁止东湖

      // 计算悬浮球位置
      let left = e.clientX - this.disX
      let top = e.clientY - this.disY

      // 限制拖拽超出X轴范围
      if (left < 0) {
        left = 0
        this.curX = left
      } else if (left > window.innerWidth - this.$el.offsetWidth) {
        left = window.innerWidth - this.$el.offsetWidth
        this.curX = left
      }
      // 限制拖拽超出Y轴范围
      if (top < 0) {
        top = 0
        this.curY = top
      } else if (top > window.innerHeight - this.$el.offsetHeight) {
        top = window.innerHeight - this.$el.offsetHeight
        this.curY = top
      }

      this.posX = left
      this.posY = top
      e.preventDefault()
    },
    // 结束拖动
    stopDrag (e) {
      // 点击事件
      if (this.isClick) {
        this.$emit('click')
      } else {
        this.doSticky(this.stickyPlacment, this.stickyDistance)
        console.log('this.stickyPlacment', this.stickyPlacment)
      }
      this.anim = true

      e.preventDefault()
      document.removeEventListener('mousemove', this.draging)
      document.removeEventListener('mouseup', this.stopDrag)
    },
    doSticky (stickyPlacment, stickyDistance) {
      const val = this.getStickyDistance(stickyPlacment, stickyDistance)
      if (stickyPlacment === 'right') {
        this.posX = window.innerWidth - this.$el.offsetWidth - val
      } else if (stickyPlacment === 'left') {
        this.posX = val
      } else if (stickyPlacment === 'bottom') {
        this.posY = window.innerHeight - this.$el.offsetHeight - val
      } else if (stickyPlacment === 'top') {
        this.posY = val
      } else if (stickyPlacment === 'autoX') {
        if (this.posX > window.innerWidth / 2) {
          this.doSticky('right', stickyDistance)
        } else {
          this.doSticky('left', stickyDistance)
        }
      } else if (stickyPlacment === 'autoY') {
        if (this.posY > window.innerHeight / 2) {
          this.doSticky('bottom', stickyDistance)
        } else {
          this.doSticky('top', stickyDistance)
        }
      }
    },
    // 获取吸附距离
    getStickyDistance (stickyPlacment, stickyDistance) {
      if (typeof stickyDistance !== 'undefined') {
        return stickyDistance
      }
      return parseFloat(this[stickyPlacment]) || 0
    }
  },
  mounted () {
    this.init()
  },
  beforeDestroy () {
    this.$el.removeEventListener('mousedown', this.startDrag)
    document.removeEventListener('mousemove', this.draging)
    document.removeEventListener('mouseup', this.stopDrag)
  }
}
</script>

<style lang='less'>
.v-float-ball {
  position: fixed;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  cursor: pointer;
  user-select: none;
  transition: left .3s, top .3s;
  z-index: 9999;
}
.v-float-ball-placeholder {
  width: 60px;
  height: 60px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  border-radius: 50%;
  background: #fff;
}
</style>
