<template>
  <view class="container">
    <button type="primary" @click="showDialog = true">打开弹窗</button>

    <!-- 弹窗遮罩层 -->
    <view v-if="showDialog" class="overlay" @click="closeDialog"></view>

    <!-- 弹窗内容 -->
    <view v-if="showDialog" class="dialog">
      <text class="dialog-title">自定义弹窗</text>
      <text class="dialog-content">这里是弹窗内容，可以自定义。</text>
      <button @click="closeDialog" type="warn" size="mini">关闭</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      showDialog: false
    }
  },
  methods: {
    closeDialog() {
      this.showDialog = false
    }
  }
}
</script>

<style scoped>
.container {
  padding: 40rpx;
}

/* 遮罩层 */
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.4);
  z-index: 999;
}

/* 弹窗框 */
.dialog {
  position: fixed;
  left: 50%;
  top: 50%;
  width: 70%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  border-radius: 20rpx;
  padding: 40rpx 30rpx;
  z-index: 1000;
  box-shadow: 0 10rpx 30rpx rgba(0, 0, 0, 0.2);
}

.dialog-title {
  font-size: 34rpx;
  font-weight: bold;
  margin-bottom: 20rpx;
  display: block;
  text-align: center;
}

.dialog-content {
  font-size: 28rpx;
  text-align: center;
  margin-bottom: 30rpx;
}
</style>