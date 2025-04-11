<template>
  <view class="container" title="分类">
    <!-- 分类导航 -->
    <view class="category-container">
      <!-- 分类 -->
      <scroll-view class="menu" scroll-y>
        <view v-for="sub in subCategories" :key="sub.id" class="sub-category">
          <view class="sub-title">{{ sub.name }}</view>
          <view class="menu-grid">
            <view
                v-for="goods in sub.goods"
                :key="goods.id"
                class="menu-item"
                @click="navigateToGoods(goods.id)"
            >
              <image :src="goods.image" class="menu-image" />
              <view class="menu-name">{{ goods.name }}</view>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      activeIndex: 0,
      subCategories: [
        {
          id: 1,
          name: '买卖求购',
          goods: [
            {id: 1, name: '买入', image: '/static/classification/buy.png'},
            {id: 2, name: '求购', image: '/static/classification/purchase.png'},
          ]
        },
        {
          id: 2,
          name: '代课服务',
          goods: [
            {id: 1, name: '代课', image: '/static/classification/substitute.png'},
            {id: 2, name: '求代', image: '/static/classification/seek.png'},
          ]
        },
        {
          id: 3,
          name: '跑腿服务',
          goods: [
            {id: 1, name: '代拿', image: '/static/classification/errand.png'},
            {id: 2, name: '外卖', image: '/static/classification/takeout.png'},
          ]
        },
        {
          id: 4,
          name: '其他',
          goods: [
            {id: 1, name: '拼单', image: '/static/classification/group.png'},
          ]
        },

      ]
    }
  },
  methods: {
    switchCategory(index) {
      this.activeIndex = index
      // 实际应加载对应分类数据
    },
    navigateToGoods(id) {
      uni.navigateTo({
        url: `/pages/goods-detail?id=${id}`
      })
    }
  }
}
</script>

<style>
.category-container {
  display: flex;
}
.menu {
  flex: 1;
  padding: 20rpx;
}
.sub-category {
  margin: 30rpx 0;
  background: #ffffff;
  border-radius: 20rpx;
  padding-top: 15rpx;
}
.sub-title {
  font-size: 32rpx;
  font-weight: bold;
  margin-bottom: 20rpx;
  padding-left: 25rpx;
}

.menu-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20rpx;
}
.menu-item {
  text-align: center;
}
.menu-image {
  width: 100rpx;
  height: 100rpx;
}
.menu-name {
  font-size: 27rpx;
  margin: 10rpx 0;
  font-weight: 700;
}
</style>
