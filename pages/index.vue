<template>
  <view class="container" title="首页">
    <!-- 搜索栏 -->
    <view class="header">
      <uni-search-bar placeholder="搜索商品" @confirm="search" />
    </view>

    <!-- 轮播图 -->
    <swiper class="banner" indicator-dots autoplay circular>
      <swiper-item v-for="(item, index) in banners" :key="index">
        <view class="banner-item">广告位{{ index + 1 }}</view>
      </swiper-item>
    </swiper>

    <!-- 商品列表 -->
    <view class="goods-list">
      <view class="goods-item" v-for="item in goodsList" :key="item.id">
        <view class="goods-name">{{ item.name }}</view>
        <view class="goods-price">¥{{ item.price }}</view>
        <button class="add-cart" @click="addToCart(item)">加入购物车</button>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      banners: [{}, {}, {}], // 模拟3个广告位
      goodsList: [
        { id: 1, name: '商品1', price: 99.9 },
        { id: 2, name: '商品2', price: 199.9 },
        { id: 3, name: '商品3', price: 299.9 },
        { id: 4, name: '商品4', price: 99.9 },
        { id: 5, name: '商品5', price: 199.9 },
        { id: 6, name: '商品6', price: 299.9 },
      ]
    }
  },
  methods: {
    search(value) {
      console.log('搜索内容：', value)
    },
    addToCart(item) {
      console.log('加入购物车：', item)
      uni.showToast({
        title: '已加入购物车',
        icon: 'none'
      })
    }
  }
}
</script>

<style>
.container {
  padding: 20rpx;
}

.banner {
  height: 300rpx;
  margin: 20rpx 0;
}

.banner-item {
  height: 100%;
  background: #f5f5f5;
  display: flex;
  align-items: center;
  justify-content: center;
}

.goods-list {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20rpx;
}

.goods-item {
  padding: 20rpx;
  background: #fff;
  border-radius: 10rpx;
  box-shadow: 0 2rpx 6rpx rgba(0,0,0,0.1);
}

.goods-name {
  font-size: 28rpx;
  margin-bottom: 10rpx;
}

.goods-price {
  color: #ff4646;
  font-size: 32rpx;
  margin: 10rpx 0;
}

.add-cart {
  background: #ff6a6a;
  color: white;
  font-size: 24rpx;
  height: 60rpx;
  line-height: 60rpx;
}
</style>
