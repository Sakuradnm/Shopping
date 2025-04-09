<template>
  <view class="container" title="我的">
    <!-- 用户信息 -->
    <view class="user-info">
      <image class="avatar" src="#" />
      <view class="user-meta">
        <view class="username">点击登录</view>
        <view class="vip-level">普通会员</view>
      </view>
    </view>

    <!-- 功能入口 -->
    <view class="function-grid">
      <view
          v-for="item in functions"
          :key="item.id"
          class="function-item"
          @click="navigateTo(item.path)"
      >
        <uni-icons :type="item.icon" size="24" />
        <text>{{ item.name }}</text>
      </view>
    </view>

    <!-- 订单状态 -->
    <view class="order-status">
      <view
          v-for="item in orderTabs"
          :key="item.status"
          class="status-item"
          @click="viewOrders(item.status)"
      >
        <view class="count">{{ item.count }}</view>
        <view class="label">{{ item.label }}</view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      functions: [
        {id: 1, name: '我的订单', icon: 'cart', path: '/pages/orders'},
        {id: 2, name: '地址管理', icon: 'location', path: '/pages/address'},
        {id: 3, name: '客服中心', icon: 'help', path: '/pages/service'}
      ],
      orderTabs: [
        {status: 1, label: '待付款', count: 0},
        {status: 2, label: '待发货', count: 0},
        {status: 3, label: '待收货', count: 0},
        {status: 4, label: '待评价', count: 0}
      ]
    }
  },
  methods: {
    navigateTo(path) {
      uni.navigateTo({url: path})
    },
    viewOrders(status) {
      uni.navigateTo({
        url: `/pages/orders?status=${status}`
      })
    }
  }
}
</script>

<style>
.user-info {
  display: flex;
  align-items: center;
  padding: 40rpx;
  background: #fff;
}

.avatar {
  width: 120rpx;
  height: 120rpx;
  border-radius: 50%;
  background: #f5f5f5;
  margin-right: 30rpx;
}

.user-meta {
  flex: 1;
}

.username {
  font-size: 36rpx;
  margin-bottom: 10rpx;
}

.vip-level {
  color: #666;
  font-size: 28rpx;
}

.function-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  padding: 30rpx 0;
  background: #fff;
  margin-top: 20rpx;
}

.function-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20rpx;
}

.order-status {
  display: flex;
  background: #fff;
  margin-top: 20rpx;
  padding: 30rpx 0;
}

.status-item {
  flex: 1;
  text-align: center;
}

.count {
  font-size: 36rpx;
  color: #ff6a6a;
}

.label {
  font-size: 28rpx;
  color: #666;
}
</style>
