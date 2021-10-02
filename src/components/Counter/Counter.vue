<template>
  <div class="number-container d-flex justify-content-center align-items-center">
    <!-- 减 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @mousedown="subCount" @mouseup="clearTimer" @touchstart="subCount" @touchend="clearTimer">-</button>
    <!-- 购买的数量 -->
    <span class="number-box">{{ count }}</span>
    <!-- 加 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @mousedown="addCount" @mouseup="clearTimer" @touchstart="addCount" @touchend="clearTimer">+</button>
  </div>
</template>

<script>
import bus from '@/components/EventBus.js'
export default {
  props: {
    count: {
      type: Number,
      default: 1
    },
    id: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      dataCount: 0,
      timer: null
    }
  },
  methods: {
    clearTimer() {
      clearInterval(this.timer)
    },
    addCount(e) {
      e.preventDefault()
      let self = this
      this.timer = setInterval(() => {
        self.dataCount = this.count
        self.dataCount++

        bus.$emit('shareNewCount', {
          id: self.id,
          count: self.dataCount
        })
      }, 100)
    },
    subCount(e) {
      e.preventDefault()
      let self = this
      this.timer = setInterval(() => {
        self.dataCount = this.count
        if (self.count === 1) {
          return
        }
        self.dataCount--

        bus.$emit('shareNewCount', {
          id: self.id,
          count: self.dataCount
        })
      }, 100)
    }
  }
}
</script>

<style lang="less" scoped>
.number-box {
  min-width: 30px;
  text-align: center;
  margin: 0 5px;
  font-size: 12px;
}

.btn-sm {
  width: 30px;
}

button:focus {
  box-shadow: none !important;
}
</style>
