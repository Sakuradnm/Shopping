<template>
  <view class="page-container">
    <!-- 主内容区域 -->
    <scroll-view class="main-content" scroll-y>
      <!-- 类型选择卡片 -->
      <view class="card type-card">
        <view class="card-title">选择发布类型</view>
        <view class="type-grid">
          <view
              v-for="item in publishTypes"
              :key="item.id"
              class="type-item"
              :class="{ 'active': selectedType === item.id }"
              @click="selectType(item.id)"
          >
            <image :src="item.image" class="type-icon" />
            <text class="type-label">{{ item.name }}</text>
          </view>
        </view>
      </view>

      <!-- 表单卡片 -->
      <view class="card form-card">
        <view class="form-group">
          <text class="form-label">发布标题</text>
          <input
              class="form-input"
              placeholder="请输入标题（5-20字）"
              v-model="formData.title"
              maxlength="20"
          />
        </view>
        <view class="form-group">
          <text class="form-label">发布内容</text>
          <textarea
              class="form-textarea"
              placeholder="详细内容（不少于10字）"
              v-model="formData.content"
              :maxlength="500"
          />
          <text class="word-count">{{ formData.content.length }}/500</text>
        </view>
        <view class="form-group">
          <text class="form-label">添加图片</text>
          <view class="upload-grid">
            <view
                v-for="(img, index) in formData.images"
                :key="index"
                class="image-preview"
            >
              <image :src="img" class="preview-image" />
              <view class="delete-mask" @click="removeImage(index)">
                <uni-icons type="close" size="20" color="#fff"></uni-icons>
              </view>
            </view>
            <view
                class="upload-btn"
                @click="uploadImage"
                v-if="formData.images.length < 3"
            >
              <uni-icons type="plus" size="24" color="#666"></uni-icons>
              <text class="upload-text">添加图片</text>
              <text class="upload-tip">最多3张</text>
            </view>
          </view>
        </view>
      </view>

      <!-- 底部操作栏 -->
      <view class="footer-bar">
        <button
            class="submit-btn"
            :class="{ 'disabled': !formValid }"
            @click="handleSubmit"
        >
          立即发布
        </button>
      </view>
    </scroll-view>
  </view>
</template>

<script>
const DEFAULT_CATEGORIES = [
  {
    goods: [
      {id: 1, name: '帖子', image: '/static/classification/posts.png'},
      {id: 2, name: '求购', image: '/static/classification/purchase.png'},
      {id: 3, name: '求代', image: '/static/classification/seek.png'},
      {id: 4, name: '代拿', image: '/static/classification/errand.png'},
    ]
  },
];

export default {
  data() {
    return {
      selectedType: null,
      formData: {
        title: '',
        content: '',
        images: []
      },
      publishTypes: [
        ...DEFAULT_CATEGORIES.flatMap(c => c.goods),
      ]
    };
  },
  computed: {
    formValid() {
      return (
          this.selectedType &&
          this.formData.title.length >= 5 &&
          this.formData.content.length >= 10
      );
    }
  },
  methods: {
    selectType(id) {
      this.selectedType = this.selectedType === id ? null : id;
    },
    async uploadImage() {
      try {
        const res = await uni.chooseImage({ count: 3 - this.formData.images.length });
        this.formData.images = [...this.formData.images, ...res.tempFilePaths];
      } catch (error) {
        uni.showToast({ title: '图片选择取消', icon: 'none' });
      }
    },
    removeImage(index) {
      this.formData.images.splice(index, 1);
    },
    async handleSubmit() {
      if (!this.formValid) {
        return uni.showToast({
          title: '请填写完整信息',
          icon: 'none'
        });
      }

      uni.showLoading({ title: '发布中...', mask: true });

      try {
        // 实际提交逻辑
        const res = await this.$api.post('/publish', {
          type: this.selectedType,
          ...this.formData
        });

        uni.hideLoading();
        uni.showToast({ title: '发布成功' });
        setTimeout(() => uni.navigateBack(), 1500);
      } catch (error) {
        uni.hideLoading();
        uni.showToast({ title: '发布失败', icon: 'none' });
      }
    }
  }
};
</script>

<style scoped lang="scss">
.page-container {
  display: flex;
}

.main-content {
  flex: 1;
  padding: 20rpx;
  width: 780rpx;
}

.card {
  background: #fff;
  border-radius: 16rpx;
  padding: 24rpx;
  margin-bottom: 24rpx;
  box-shadow: 0 4rpx 12rpx rgba(0,0,0,0.05);
}

.card-title {
  font-size: 32rpx;
  font-weight: 600;
  color: #333;
  margin-bottom: 24rpx;
}

.type-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20rpx;
}

.type-item {
  padding: 24rpx;
  border: 2rpx solid #eee;
  border-radius: 12rpx;
  text-align: center;
  transition: all 0.2s;

  &.active {
    border-color: #007AFF;
    background: rgba(0,122,255,0.05);
    transform: translateY(-4rpx);
  }
}

.type-icon {
  width: 80rpx;
  height: 80rpx;
  margin-bottom: 12rpx;
}

.type-label {
  font-size: 24rpx;
  color: #666;
  display: block;
}

.form-group {
  margin-bottom: 32rpx;
}

.form-label {
  display: block;
  font-size: 28rpx;
  color: #333;
  margin-bottom: 16rpx;
  font-weight: 500;
}

.form-input {
  height: 80rpx;
  padding: 0 20rpx;
  border: 2rpx solid #eee;
  border-radius: 8rpx;
  font-size: 28rpx;
}

.form-textarea {
  width: 100%;
  height: 240rpx;
  padding: 20rpx;
  border: 2rpx solid #eee;
  border-radius: 8rpx;
  font-size: 28rpx;
  box-sizing: border-box;
}

.word-count {
  display: block;
  text-align: right;
  font-size: 24rpx;
  color: #999;
  margin-top: 8rpx;
}

.upload-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20rpx;
}

.image-preview {
  width: 200rpx;
  height: 200rpx;
  border-radius: 8rpx;
  position: relative;
  overflow: hidden;
}

.preview-image {
  width: 100rpx;
  height: 100rpx;
  object-fit: cover;
}

.delete-mask {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.upload-btn {
  width: 200rpx;
  height: 200rpx;
  border: 2rpx dashed #ccc;
  border-radius: 8rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.upload-text {
  font-size: 24rpx;
  color: #666;
  margin-top: 12rpx;
}

.upload-tip {
  font-size: 22rpx;
  color: #999;
}

.footer-bar {
  border-radius: 16rpx;
  padding: 20rpx;
  background: #fff;
  box-shadow: 0 -4rpx 12rpx rgba(0,0,0,0.05);
}

.submit-btn {
  background: #007AFF;
  color: white;
  height: 88rpx;
  border-radius: 44rpx;
  font-size: 32rpx;
  display: flex;
  align-items: center;
  justify-content: center;

  &.disabled {
    background: #ccc;
    opacity: 0.7;
  }
}
</style>
