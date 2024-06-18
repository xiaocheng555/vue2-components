<template>
  <div
    class="pull-refresh"
    :class="{ 'pull-animate': !startY }"
    @transitionend="handleTransitionEnd">
    <div v-if="isRefreshing" class="loading"></div>
    <div v-else class="arrow" :style="{ transform: canRelease ? 'rotate(180deg)' : '' }" ></div>
    <div class="pull-content">
      {{ canRelease ? (isRefreshing ? 'Refreshing…' : 'Release to Refresh') : 'Pull down to refresh' }}
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      canRelease: false,
      startY: 0,
      isPulling: false,
      isRefreshing: false,
      container: null
    }
  },
  methods: {
    /** 初始化 */
    async init () {
      await this.$nextTick()
      this.container = this.$el.parentNode

      this.container.addEventListener('mousedown', this.startPull)
      this.container.addEventListener('touchstart', this.startPull, { passive: false })
      this.container.addEventListener('mousemove', this.pulling)
      this.container.addEventListener('touchmove', this.pulling, { passive: false })
      for (const event of ['mouseup', 'mouseleave', 'touchend', 'touchcancel']) {
        this.container.addEventListener(event, this.endPull)
      }
    },
    /** 開始下拉 */
    startPull (e) {
      // 非置頂，禁止下拉更新
      if (this.container.scrollTop > 0 || this.isRefreshing) return

      this.startY = (e.clientY ? e.clientY : e.touches[0].clientY) || 0
    },
    /** 下拉過程 */
    pulling (e) {
      if (!this.startY || this.isRefreshing) return

      const y = (e.clientY ? e.clientY : e.touches[0].clientY) || 0
      let height = y - this.startY
      // 若不是下拉刷新
      if (!this.container.scrollTop && height < 0) {
        this.startY = 0
        this.container.style.setProperty('overflow', '')
        this.isPulling = false
        return
      }
      if (height) e.preventDefault()
      if (height < 10) return

      // 因為ios置頂時下拉會抖動
      this.container.style.setProperty('overflow', 'hidden', 'important')
      height -= 10
      this.canRelease = height > 40
      this.isPulling = true
      this.$el.style.height = `${this.canRelease && height > 0 ? Math.sqrt(height * 40) : height}px`
    },
    /** 結束下拉，開始刷新 */
    endPull () {
      console.log('endPull')
      if (!this.startY || this.isRefreshing) return

      this.$el.style.height = `${this.canRelease ? 40 : 0}px`
      if (this.canRelease) {
        this.$emit('refresh', this.refreshSuccess)
        this.isRefreshing = true
      }
      this.container.style.setProperty('overflow', '')
      this.startY = 0
    },
    /** 刷新成功 */
    refreshSuccess () {
      this.$el.style.height = 0
      this.isPulling = false
    },
    /** 動畫結束 */
    handleTransitionEnd () {
      if (!this.isPulling) {
        this.canRelease = false
        this.isRefreshing = false
      }
    },
    /** 停止下拉 */
    stopPull () {
      this.container.style.setProperty('overflow', '')
      this.startY = 0
      this.canRelease = false
      this.isPulling = false
      this.isRefreshing = false
      this.$el.style.height = 0
    }
  },
  mounted () {
    this.init()
  }
}
</script>

<style lang='less' scoped>
.pull-refresh {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
  overflow: hidden;
  height: 0;
  color: #2496FF;
}

.pull-animate {
  transition: height 0.3s;
}

.pull-content {
  line-height: 20px;
  letter-spacing: 0.3px;
  font-size: 14px;
  font-weight: bold;
}

.arrow {
  position: relative;
  top: -3px;
  width: 2px;
  height: 12px;
  border-right: 2px solid #2496FF;
  box-sizing: content-box;
  transition: transform ease 0.5s;
  &::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    border: 3px solid #2496FF;
    border-top-color: transparent;
    border-left-color: transparent;
    transform: rotate(45deg);
    border-radius: 1px;
  }
}

.loading {
  flex-shrink: 0;
  width: 14px;
  height: 14px;
  margin-bottom: 2px;
  border-radius: 50%;
  border: 2px solid #2496FF;
  border-left-color: transparent;
  animation: 0.8s cubic-bezier(0.45, 0.05, 0.55, 0.95) infinite loading;
}

@keyframes loading {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(359deg);
  }
}
</style>
