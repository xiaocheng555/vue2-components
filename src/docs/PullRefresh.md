## PullRefresh 上拉加载

### 基本用法

:::demo 
```html
<template>
  <div class="box">
    <pull-refresh @refresh="refresh"></pull-refresh>
    下拉加载
  </div>
</template>
<script>
import PullRefresh from '@/components/PullRefresh.vue'

export default {
  components: {
    PullRefresh
  },
  methods: {
    refresh (done) {
      setTimeout(() => {
        done()
      }, 2000)
    }
  }
}
</script>
<style scoped>
.box {
  height: 300px;
}
</style>
```
:::

