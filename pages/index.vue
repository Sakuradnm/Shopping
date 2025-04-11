<template>
  <view class="container">
    <!-- 固定搜索栏 -->
    <view class="header">
      <uni-search-bar
          placeholder="搜索帖子/用户"
          @confirm="search"
          :radius="30"
      />
    </view>

    <!-- 广告轮播 -->
    <swiper class="banner" indicator-dots autoplay circular>
      <swiper-item v-for="(item, index) in banners" :key="index">
        <image :src="item.image" class="banner-image" mode="aspectFill"/>
      </swiper-item>
    </swiper>

    <!-- 帖子列表 -->
    <view class="post-list">
      <view
          v-for="post in postList"
          :key="post.id"
          class="post-card"
      >
        <!-- 用户信息 -->
        <view class="user-info">
          <image :src="post.avatar" class="avatar"/>
          <view>
            <text class="username">{{ post.username }}</text>
            <text class="time">{{ post.createTime }}</text>
          </view>
        </view>

        <!-- 帖子内容 -->
        <view class="post-content">
          <text class="title">{{ post.title }}</text>
          <text class="content">{{ post.content }}</text>
          <view v-if="post.images.length" class="image-grid">
            <image
                v-for="(img, idx) in post.images"
                :key="idx"
                :src="img"
                mode="aspectFill"
                class="post-image"
            />
          </view>
        </view>

        <!-- 互动栏 -->
        <view class="action-bar">
          <view class="action-item">
            <uni-icons type="hand-up" size="18" color="#666"/>
            <text>{{ post.likes }}</text>
          </view>
          <view class="action-item">
            <uni-icons type="chat" size="18" color="#666"/>
            <text>{{ post.comments }}</text>
          </view>
          <view class="action-item">
            <uni-icons type="redo" size="18" color="#666"/>
            <text>{{ post.shares }}</text>
          </view>
        </view>
      </view>

      <!-- 加载更多 -->
      <view class="loading-status">
        <text v-if="loading">正在加载...</text>
        <text v-else-if="!hasMore">没有更多内容了</text>
      </view>
    </view>

    <view
        class="back-top"
        v-show="showBackTop"
        @click="scrollToTop"
    >
      <uni-icons type="arrow-up" size="24" color="#fff"></uni-icons>
    </view>
  </view>
</template>


<script>
export default {
  data() {
    return {
      banners: [
        { image: '/static/demo.gif' },
        { image: '/static/demo.gif' }
      ],
      postList: [],
      page: 1,
      loading: false,
      hasMore: true,
      pageSize: 10,
      loadCount: 0,
      isFirstLoad: true,
      showBackTop: false, // 新增返回顶部按钮显示状态
    }
  },
  onLoad() {
    this.loadPosts()
  },
  onReachBottom() {
    if (this.hasMore) {
      this.page++
      this.loadPosts()
    }
  },
  onPageScroll(e) {
    // 添加防抖处理
    clearTimeout(this.timer)
    this.showBackTop = e.scrollTop > 200
    this.timer = setTimeout(() => {
      this.checkScroll()
    }, 300)
  },
  methods: {
    //一键回到顶部
    scrollToTop() {
      uni.pageScrollTo({
        scrollTop: 0,
        duration: 300
      })
    },

    async loadPosts() {
      this.loading = true
      try {
        // 动态计算每页数量
        const dynamicPageSize = this.isFirstLoad ? 10 : 5

        // 模拟API请求
        const newPosts = this.mockPosts(dynamicPageSize)

        this.postList = [...this.postList, ...newPosts]
        this.hasMore = newPosts.length === dynamicPageSize

        // 更新加载状态
        this.loadCount++
        if (this.loadCount >= 2) {
          this.isFirstLoad = false
        }
      } finally {
        this.loading = false
      }
    },
    checkScroll() {
      const query = uni.createSelectorQuery().in(this)
      query.select('.post-list').boundingClientRect()
      query.exec(res => {
        if (res[0].bottom < uni.getSystemInfoSync().windowHeight + 100) {
          this.onReachBottom()
        }
      })
    },
    mockPosts(pageSize) {
      // 生成测试数据（根据实际需求修改）
      return Array.from({length: pageSize}, (_, i) => ({
        id: Date.now() + i,
        title: `测试标题${this.page}-${i}`,
        content: '测试内容'.repeat(10),
        images: ['/static/demo.gif'],
        username: '用户' + (this.page * pageSize + i),
        avatar: '/static/my.png',
        createTime: `${this.page}小时前`,
        likes: Math.floor(Math.random() * 1000),
        comments: Math.floor(Math.random() * 500),
        shares: Math.floor(Math.random() * 300)
      }))
    }
  }
}
</script>

<style>
.container {
  padding: 20rpx;
}

.header {
  position: sticky;
  top: 0;
  z-index: 100;
  background: #fff;
  padding: 20rpx;
  border-radius: 12rpx;
}

.banner {
  height: 360rpx;
  border-radius: 12rpx;
  align-items: center;
  width: 90%;
  margin: 0 auto;
}
.banner-image {
  margin-top: 10rpx;
  width: 100%;
  height: 100%;
  border-radius: 12rpx;
  object-fit: cover;
  display: block;
}

.post-card {
  background: #fff;
  margin: 30rpx 20rpx;
  padding: 24rpx;
  border-radius: 12rpx;
  box-shadow: 0 4rpx 12rpx rgba(0,0,0,0.05);
}

.user-info {
  display: flex;
  align-items: center;
  margin-bottom: 24rpx;
}
.title {
  display: grid;
  font-size: 32rpx;
  margin-bottom: 10rpx;
}
.content {
  font-size: 28rpx;
  margin-bottom: 20rpx;
}
.user-info .avatar {
  width: 80rpx;
  height: 80rpx;
  border-radius: 50%;
  margin-right: 20rpx;
}
.user-info .username {
  font-weight: 500;
  margin-right: 20rpx;
}
.user-info .time {
  color: #999;
  font-size: 24rpx;
}

.image-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10rpx;
  margin-top: 20rpx;
}
.image-grid .post-image {
  width: 100%;
  height: 240rpx;
  border-radius: 8rpx;
}

.action-bar {
  display: flex;
  justify-content: space-around;
  margin-top: 30rpx;
}
.action-item {
  display: flex;
  align-items: center;
}
.action-item text {
  margin-left: 10rpx;
  color: #666;
}
.loading-status {
  text-align: center;
  padding: 20rpx;
  color: #999;
  font-size: 28rpx;
}

.back-top {
  position: fixed;
  right: 30rpx;
  bottom: 100rpx;
  width: 80rpx;
  height: 80rpx;
  background: rgb(255, 106, 106);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4rpx 12rpx rgba(0,0,0,0.15);
  z-index: 999;
  transition: all 0.3s;
}

.back-top:active {
  transform: scale(0.9);
  background: #ff4b4b;
}
</style>
