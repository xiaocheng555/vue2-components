## FloatBall 悬浮球

### 基本用法

:::demo 
```html
<template>
  <div>
    <el-radio-group v-model="type">
      <el-radio label="none">不吸附</el-radio>
      <el-radio label="left">吸附左边</el-radio>
      <el-radio label="right">吸附右边</el-radio>
      <el-radio label="top">吸附上边</el-radio>
      <el-radio label="bottom">吸附下边</el-radio>
      <el-radio label="autoX">自动吸附左、右边</el-radio>
      <el-radio label="autoY">自动吸附上、下边</el-radio>
    </el-radio-group>
    <float-ball 
      class="float-ball" 
      right="20px" 
      bottom="150px" 
      :sticky-placment="type"
      :sticky-distance="20"
      :size="100"
      @click="handleClick">
      悬浮球
    </float-ball>
  </div>
</template>
<script>
import FloatBall from '@/components/FloatBall.vue'

export default {
  components: {
    FloatBall
  },
  data () {
    return {
      type: 'autoX'
    }
  },
  methods: {
    handleClick () {
      console.log('悬浮球被点击了')
    }
  }
}
</script>
<style scoped>
.float-ball {
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  background: #fff;
}
</style>
```
:::

