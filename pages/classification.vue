<template>
  <view class="container" title="分类">
    <!-- 分类导航 -->
    <view class="category-container">
      <!-- 左侧分类菜单 -->
      <scroll-view class="left-menu" scroll-y>
        <view
            v-for="(item, index) in categories"
            :key="item.id"
            class="menu-item"
            :class="{ active: activeIndex === index }"
            @click="switchCategory(index)"
        >
          {{ item.name }}
        </view>
      </scroll-view>

      <!-- 右侧子分类 -->
      <scroll-view class="right-content" scroll-y>
        <view v-for="sub in subCategories" :key="sub.id" class="sub-category">
          <view class="sub-title">{{ sub.name }}</view>
          <view class="goods-grid">
            <view
                v-for="goods in sub.goods"
                :key="goods.id"
                class="goods-item"
                @click="navigateToGoods(goods.id)"
            >
              <image :src="goods.thumb" class="goods-image" />
              <view class="goods-name">{{ goods.name }}</view>
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
      categories: [
        {id: 1, name: '手机数码'},
        {id: 2, name: '家用电器'},
        {id: 3, name: '服装鞋包'}
      ],
      subCategories: [
        {
          id: 1,
          name: '手机/平板',
          goods: [
            {id: 1, name: '小米15', thumb: '#'},
            {id: 2, name: 'OPPO find x8', thumb: '#'},
            {id: 3, name: '苹果16', thumb: '#'},
            {id: 4, name: '小米6s pro', thumb: '#'},
            {id: 5, name: 'ipad 2024', thumb: '#'},
          ]
        },
        {
          id: 2,
          name: '大众电器',
          goods: [
            {id: 1, name: '冰箱', thumb: '#'},
            {id: 2, name: '空调', thumb: '#'},
            {id: 3, name: '电视', thumb: '#'},
            {id: 4, name: '洗衣机', thumb: '#'},
            {id: 5, name: '微波炉', thumb: '#'},
          ]
        },
        {
          id: 3,
          name: '潮鞋衣装',
          goods: [
            {id: 1, name: '裤子', thumb: '#'},
            {id: 2, name: '裙子', thumb: '#'},
            {id: 3, name: '上衣', thumb: '#'},
            {id: 4, name: '鞋子', thumb: '#'},
            {id: 5, name: '首饰', thumb: '#'},
          ]
        }
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
  height: calc(100vh - 60px);
}

.left-menu {
  width: 200rpx;
  background: #f8f8f8;
}

.menu-item {
  padding: 30rpx;
  font-size: 28rpx;
  border-left: 4rpx solid transparent;

  &.active {
    background: #fff;
    border-color: #ff6a6a;
    color: #ff6a6a;
  }
}

.right-content {
  flex: 1;
  padding: 20rpx;
}

.sub-title {
  font-size: 32rpx;
  font-weight: bold;
  margin: 20rpx 0;
}

.goods-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20rpx;
}

.goods-item {
  text-align: center;
}

.goods-image {
  width: 150rpx;
  height: 150rpx;
  background: #f5f5f5;
  border-radius: 8rpx;
}

.goods-name {
  font-size: 24rpx;
  margin-top: 10rpx;
}
</style>
