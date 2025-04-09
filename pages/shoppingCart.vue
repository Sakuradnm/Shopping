<template>
  <view class="container" title="购物车">
    <!-- 购物车列表 -->
    <view class="cart-list">
      <view
          v-for="item in cartList"
          :key="item.id"
          class="cart-item"
      >
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
    </view>

    <!-- 底部结算栏 -->
    <view class="settlement-bar">
      <view class="select-all">
        <checkbox :checked="allChecked" @click="toggleAllCheck" />
        全选
      </view>
      <view class="total-price">
        合计：¥{{ totalPrice }}
      </view>
      <button class="btn-settle">去结算({{ checkedCount }})</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      cartList: [
        {
          id: 1,
          name: '商品1',
          price: 99.9,
          quantity: 1,
          checked: true,
          thumb: '#'
        }
      ]
    }
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
    }
  },
  methods: {
    toggleCheck(id) {
      const item = this.cartList.find(item => item.id === id)
      if (item) item.checked = !item.checked
    },
    toggleAllCheck() {
      const newState = !this.allChecked
      this.cartList.forEach(item => item.checked = newState)
    },
    changeQuantity(id, delta) {
      const item = this.cartList.find(item => item.id === id)
      if (item) {
        item.quantity = Math.max(1, item.quantity + delta)
      }
    }
  }
}
</script>

<style>
.cart-item {
  display: flex;
  padding: 20rpx;
  background: #fff;
  margin-bottom: 20rpx;
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
}

.btn-minus, .btn-plus {
  width: 50rpx;
  height: 50rpx;
  line-height: 50rpx;
  font-size: 32rpx;
}

.quantity-input {
  width: 80rpx;
  text-align: center;
  margin: 0 10rpx;
}

.settlement-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  height: 100rpx;
  background: #fff;
  display: flex;
  align-items: center;
  padding: 0 20rpx;
  border-top: 1rpx solid #eee;
}

.select-all {
  display: flex;
  align-items: center;
  margin-right: auto;
}

.total-price {
  margin-right: 30rpx;
  color: #ff4646;
  font-weight: bold;
}

.btn-settle {
  background: #ff6a6a;
  color: white;
  height: 70rpx;
  line-height: 70rpx;
  min-width: 200rpx;
}
</style>
