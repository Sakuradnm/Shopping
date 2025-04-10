<template>
  <view class="container" title="购物车">
    <!-- 购物车列表 -->
    <view class="cart-list">
      <view v-if="cartList.length === 0" class="empty-cart">
        <image src="/static/dog2.gif" class="empty-icon" mode="widthFix"/>
        <text class="empty-text">购物车空空如也，快去逛逛吧~</text>
      </view>
      <view v-else>
        <view
            v-for="(item, index) in cartList"
            :key="item.id"
            class="cart-item"
            @touchstart="touchStart($event, item.id)"
            @touchmove="touchMove($event, item.id)"
            @touchend="touchEnd($event, item.id)"
            :style="{ transform: `translateX(${item.translateX || 0}px)` }"
        >
          <view class="item-content">
            <view class="item-left">
              <checkbox :checked="item.checked" @click="toggleCheck(item.id)" />
              <image :src="item.thumb" class="goods-thumb" />
            </view>
            <view class="item-right">
              <view class="goods-name">{{ item.name }}</view>
              <view class="goods-price">¥{{ item.price }}</view>
              <view class="quantity-control">
                <button class="btn-minus" @click="changeQuantity(item.id, -1)">-</button>
                <input class="quantity-input" :value="item.quantity" />
                <button class="btn-plus" @click="changeQuantity(item.id, 1)">+</button>
              </view>
            </view>
          </view>

          <view class="slide-actions">
            <view class="btn-delete" @click="handleDelete(item.id)">删除</view>
          </view>
        </view>
      </view>

      <!-- 结算栏 -->
      <view class="settlement-bar">
        <view class="select-all">
          <checkbox :checked="allChecked" @click="toggleAllCheck" />
          全选
        </view>
        <view class="price-info" v-if="!showDelete">
          <span class="total-quantity">已选{{ checkedCount }}件,</span>
          合计：
          <span class="total-price">¥{{ totalPrice }}</span>
        </view>
        <button
            class="btn-settle"
            v-if="!showDelete"
            :class="{ disabled: checkedCount === 0 }"
            @click="handleSettle"
        >
          结算({{ checkedCount }})
        </button>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      startX: 0,
      currentX: 0,
      slideThreshold: 60, // 滑动阈值
      cartList: [
        {
          id: 1,
          name: '商品1',
          price: 99.9,
          quantity: 1,
          checked: true,
          thumb: '#',
          translateX: 0 // 添加初始化
        }
      ],
      showDeleteButton: false
    }
  },
  // 添加生命周期
  onShow() {
    this.loadCart()
  },

  computed: {
    totalPrice() {
      return this.cartList
          .filter(item => item.checked)
          .reduce((sum, item) => sum + item.price * item.quantity, 0)
          .toFixed(2)
    },
    checkedCount() {
      return this.cartList.filter(item => item.checked).length
    },
    allChecked() {
      return this.cartList.every(item => item.checked)
    },
    showDelete: {
      get() {
        return this.showDeleteButton
      },
      set(value) {
        this.showDeleteButton = value
      }
    }
  },

  methods: {
    // 触摸事件处理
    touchStart(e, id) {
      this.startX = e.touches[0].clientX
      this.currentX = this.startX
      // 关闭其他打开的滑动项
      this.cartList.forEach(item => {
        if (item.id !== id) {
          item.translateX = 0
        }
      })
    },
    touchMove(e, id) {
      this.currentX = e.touches[0].clientX
      const diffX = this.startX - this.currentX
      const item = this.cartList.find(item => item.id === id)
      if (diffX > 0) {
        item.translateX = -Math.min(diffX, 120)
      }
    },
    touchEnd(e, id) {
      const diffX = this.startX - this.currentX
      const item = this.cartList.find(item => item.id === id)
      if (diffX > this.slideThreshold) {
        item.translateX = -120
      } else {
        item.translateX = 0
      }
    },

    // 添加结算方法
    handleSettle() {
      if (this.checkedCount === 0) {
        uni.showToast({
          title: '请选择要结算的商品',
          icon: 'none'
        })
        return
      }
      const selectedItems = this.cartList.filter(item => item.checked)
      console.log('结算商品：', selectedItems)
      // 这里可以跳转到结算页面
    },
    // 新增方法
    loadCart() {
      this.cartList = (uni.getStorageSync('cart') || []).map(item => {
        return {
          ...item,
          translateX: 0 // 确保每个商品都有这个属性
        }
      })
    },
    // 新增删除方法
    deleteItem(id) {
      this.cartList = this.cartList.filter(item => item.id !== id)
      uni.setStorageSync('cart', this.cartList)
      this.$forceUpdate()
    },
    handleDelete(id) {
      uni.showModal({
        title: '确认删除',
        content: '确定要删除该商品吗？',
        success: res => {
          if (res.confirm) {
            this.deleteItem(id)
          } else {
            this.cartList.find(item => item.id === id).translateX = 0
          }
        }
      })
    },
    toggleCheck(id) {
      const item = this.cartList.find(item => item.id === id)
      if (item) item.checked = !item.checked
      uni.setStorageSync('cart', this.cartList)
    },
    toggleAllCheck() {
      const newState = !this.allChecked
      this.cartList.forEach(item => item.checked = newState)
      uni.setStorageSync('cart', this.cartList)
    },
    changeQuantity(id, delta) {
      const item = this.cartList.find(item => item.id === id)
      if (item) {
        item.quantity = Math.max(1, item.quantity + delta)
      }
      uni.setStorageSync('cart', this.cartList)
    }
  }
}
</script>

<style>
.empty-cart {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 100rpx 0;
  margin-top: 50rpx;
}
.empty-icon {
  width: 300rpx;
  margin-bottom: 80rpx;
  animation: fadeIn 0.6s ease-in;
}
.empty-text {
  color: #888;
  font-size: 30rpx;
}


.cart-item {
  display: flex;
  padding: 20rpx;
  background: #fff;
  margin: 20rpx 10rpx;
  position: relative;
  transition: transform 0.3s ease;
  border-radius: 20rpx;
}

.item-content {
  width: 100%;
  background: #fff;
  display: flex;
  padding: 20rpx;
}

.item-left {
  display: flex;
  align-items: center;
  margin-right: 20rpx;
}
.goods-thumb {
  width: 150rpx;
  height: 150rpx;
  background: #f5f5f5;
  margin-left: 20rpx;
}

.item-right {
  flex: 1;
}
.quantity-control {
  display: flex;
  align-items: center;
  margin-top: 20rpx;
  padding-left: 150rpx;
}
.btn-minus, .btn-plus {
  margin: 0;
  padding: 0 20rpx;
  line-height: 50rpx;
  font-size:26rpx;
}
.quantity-input {
  width: 60rpx;
  text-align: center;
  margin: 0 20rpx;
  border-bottom: 1rpx solid #b1b1b0;
}

.settlement-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  height: 120rpx;
  background: #fff;
  display: flex;
  align-items: center;
  padding: 0 30rpx;
  box-shadow: 0 -4rpx 12rpx rgba(0,0,0,0.06);
  z-index: 100;
}

.btn-settle {
  background: #ff6a6a;
  color: white;
  height: 80rpx;
  min-width: 200rpx;
  border-radius: 40rpx;
  font-size: 32rpx;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  justify-content: center;
}
.btn-settle:active {
  opacity: 0.8;
  transform: scale(0.98);
}
.btn-settle.disabled {
  background: #cccccc;
  opacity: 0.7;
}

.slide-actions {
  position: absolute;
  right: -80px;
  top: 0;
  bottom: 0;
  width: 80px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding-left: 20rpx;
}
.btn-delete {
  background: #ff4646;
  color: white;
  padding: 0 30rpx;
  height: 70%;
  border-radius: 35rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.select-all {
  display: flex;
  align-items: center;
  margin-right: auto;
}
.price-info {
  text-align: right;
  margin: 0 20rpx;
}
.total-price {
  color: #ff0000;
  font-size: 34rpx;
  font-weight: bold;
}
.total-quantity {
  color: #b1b1b0;
  font-size: 24rpx;
  padding-right: 10rpx;
}







/* 勾选状态 */
checkbox .wx-checkbox-input,
checkbox .uni-checkbox-input {
  border-radius: 50% !important;
}

checkbox .wx-checkbox-input.wx-checkbox-input-checked,
checkbox .uni-checkbox-input.uni-checkbox-input-checked {
  background: #ff6a6a !important;
}

</style>
