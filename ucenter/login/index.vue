<template>
  <view class="login-container">
    <!-- 登录头部 -->
    <view class="login-header">
      <image
          class="title-gif"
          src="/static/dog.gif"
          mode="widthFix"
      />
    </view>

    <!-- 一键登录表单 -->
    <view class="form-container" v-if="loginType === 'quick'">
      <button
          class="quick-login-btn"
          type="primary"
          @click="handleQuickLogin"
      >
        本机号码一键登录/注册
      </button>
    </view>

    <!-- 其他登录方式入口 -->
    <view class="switch-login" @click="showLoginMethods = true">
      <text>其他登录方式</text>
    </view>

    <!-- 协议声明 -->
    <view class="protocol-checkbox">
      <label class="checkbox" @click="toggleAgree">
        <view class="custom-checkbox" :class="{ checked: agreeProtocol }">
          <text v-if="agreeProtocol" class="checkmark">✓</text>
        </view>
        我已阅读并同意<text class="protocol-link">《服务协议》</text>和<text class="protocol-link">《隐私权政策》</text>
      </label>
    </view>

    <!-- 登录方式选择弹窗 -->
    <uni-popup ref="popup" type="bottom">
      <view class="login-methods">
        <view class="method-item" @click="switchLoginType('sms')">
          手机验证码登录
        </view>
        <view class="method-item" @click="switchLoginType('password')">
          账号密码登录
        </view>
        <view class="method-item cancel" @click="showLoginMethods = false">
          取消
        </view>
      </view>
    </uni-popup>
  </view>
</template>

<script>
export default {
  data() {
    return {
      loginType: 'quick',
      agreeProtocol: false,
      showLoginMethods: false
    }
  },
  watch: {
    showLoginMethods(val) {
      val ? this.$refs.popup.open() : this.$refs.popup.close()
    }
  },
  methods: {
    toggleAgree() {
      this.agreeProtocol = !this.agreeProtocol
    },

    switchLoginType(type) {
      this.loginType = type
      this.showLoginMethods = false
    },

    async handleQuickLogin() {
      if (!this.agreeProtocol) {
        uni.showModal({
          title: '提示',
          content: '请先阅读并同意用户协议和隐私政策',
          showCancel: false
        })
        return
      }
      // 执行一键登录逻辑
    }
  }
}
</script>

<style scoped lang="scss">
.login-container {
  display: flex;
  flex-direction: column;
  height: 70vh;

  .login-header {
    text-align: center;
    margin-bottom: 80rpx;

    .title-gif {
      width: 500rpx;
      height: auto;
      display: block;
      margin: 40rpx auto;
      animation: fadeIn 0.6s ease-in;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-20rpx); }
      to { opacity: 1; transform: translateY(0); }
    }
  }

  .form-container {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;

    .quick-login-btn {
      background: #FF6A6A;
      width: 80%;
      height: 100rpx;
      line-height: 100rpx;
      border-radius: 50rpx;
      font-size: 32rpx;
    }
  }

  .protocol-checkbox {
    position: fixed;
    bottom: 160rpx;
    width: 100%;
    text-align: center;
    font-size: 24rpx;

    .checkbox {
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .custom-checkbox {
      width: 36rpx;
      height: 36rpx;
      border: 2rpx solid #007AFF;
      border-radius: 50%;
      margin-right: 16rpx;
      display: flex;
      align-items: center;
      justify-content: center;

      &.checked {
        background-color: #007AFF;
      }

      .checkmark {
        color: white;
        font-size: 24rpx;
      }
    }

    .protocol-link {
      color: #007AFF;
      margin: 0 10rpx;
    }
  }

  .switch-login {
    position: fixed;
    bottom: 240rpx;
    width: 100%;
    text-align: center;
    color: #007AFF;
    font-size: 28rpx;
  }

  .login-methods {
    background: #fff;
    border-radius: 24rpx 24rpx 0 0;
    padding: 30rpx;

    .method-item {
      padding: 30rpx;
      text-align: center;
      border-bottom: 1rpx solid #eee;

      &:last-child {
        border: none;
      }

      &.cancel {
        color: #999;
        margin-top: 20rpx;
      }
    }
  }
}
</style>
