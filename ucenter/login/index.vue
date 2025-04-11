<template>
  <view class="login-container">
    <!-- 登录头部 -->
    <view class="login-header">
      <image
          class="title-gif"
          src="/static/gif/dog.gif"
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

    <!-- 手机验证码登录 -->
    <view v-if="loginType === 'sms'" class="sms-login-form">
      <input
          v-model="smsForm.phone"
          placeholder="请输入手机号码"
          type="number"
          class="custom-input"
          placeholder-class="input-placeholder"
      />
      <view class="code-input-group">
        <input
            v-model="smsForm.code"
            placeholder="请输入验证码"
            type="number"
            class="custom-input code-input"
            placeholder-class="input-placeholder"
        />
        <button
            class="get-code-btn"
            :class="{ disabled: isCounting }"
            @click="getSMSCode"
        >
          {{ codeButtonText }}
        </button>
      </view>
      <button class="login-btn" @click="handleSmsLogin">登录</button>
    </view>

    <!-- 密码登录 -->
    <view v-if="loginType === 'password'" class="password-login-form">
      <input
          v-model="passwordForm.account"
          placeholder="请输入手机号/邮箱"
          class="custom-input"
          placeholder-class="input-placeholder"
      />
      <input
          v-model="passwordForm.password"
          placeholder="请输入密码"
          :password="true"
          class="custom-input"
          placeholder-class="input-placeholder"
      />
      <view class="forgot-password" @click="navigateToForgot">
        <text>忘记密码？</text>
      </view>
      <button class="login-btn" @click="handlePasswordLogin">登录</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      loginType: 'quick',
      agreeProtocol: false,
      showLoginMethods: false,
      smsForm: {
        phone: '',
        code: ''
      },
      passwordForm: {
        account: '',
        password: ''
      },
      isCounting: false,
      countdown: 60,
      codeButtonText: '获取验证码'
    }
  },
  watch: {
    showLoginMethods(val) {
      val ? this.$refs.popup.open() : this.$refs.popup.close()
    }
  },
  methods: {
    // 获取短信验证码
    async getSMSCode() {
      if (!this.validatePhone()) return
      if (this.isCounting) return

      try {
        const res = await uni.request({
          url: '/api/sms/send',
          method: 'POST',
          data: { phone: this.smsForm.phone }
        })
        if (res.code === 200) {
          this.startCountdown()
          uni.showToast({ title: '验证码已发送', icon: 'success' })
        }
      } catch (error) {
        uni.showToast({ title: '发送失败，请重试', icon: 'none' })
      }
    },
    // 启动倒计时
    startCountdown() {
      this.isCounting = true
      const timer = setInterval(() => {
        if (this.countdown <= 0) {
          clearInterval(timer)
          this.isCounting = false
          this.codeButtonText = '重新获取'
          this.countdown = 60
          return
        }
        this.countdown--
        this.codeButtonText = `${this.countdown}s后重试`
      }, 1000)
    },
    // 手机号验证
    validatePhone() {
      if (!/^1[3-9]\d{9}$/.test(this.smsForm.phone)) {
        uni.showToast({ title: '手机号格式错误', icon: 'none' })
        return false
      }
      return true
    },

    // 短信登录处理
    async handleSmsLogin() {
      if (!this.validateForm('sms')) return
      // 调用短信登录API
    },

    // 密码登录处理
    async handlePasswordLogin() {
      if (!this.validateForm('password')) return
      // 调用密码登录API
    },
    // 表单验证
    validateForm(type) {
      if (!this.agreeProtocol) {
        this.showProtocolAlert()
        return false
      }

      if (type === 'sms') {
        if (!this.smsForm.phone) {
          uni.showToast({ title: '请输入手机号', icon: 'none' })
          return false
        }
        if (!this.smsForm.code) {
          uni.showToast({ title: '请输入验证码', icon: 'none' })
          return false
        }
      } else if (type === 'password') {
        if (!this.passwordForm.account) {
          uni.showToast({ title: '请输入账号', icon: 'none' })
          return false
        }
        if (!this.passwordForm.password) {
          uni.showToast({ title: '请输入密码', icon: 'none' })
          return false
        }
      }
      return true
    },
    // 跳转忘记密码
    navigateToForgot() {
      uni.navigateTo({ url: '/pages/ucenter/forgot/index' })
    },


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

  .login-header {
    text-align: center;
    margin-bottom: 60rpx;

    .title-gif {
      width: 500rpx;
      height: auto;
      display: block;
      margin: 40rpx auto 0;
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
// 新增样式
.custom-input {
  width: 70%;
  margin: 30rpx auto;
  height: 100rpx;
  padding: 0 40rpx;
  background: rgb(255, 255, 255);
  box-shadow: 0 0 5rpx rgb(255, 106, 106);
  border-radius: 50rpx;
  font-size: 32rpx;
  -webkit-appearance: none;
}

.input-placeholder {
  color: #999;
  font-size: 32rpx;
}

.code-input-group {
  position: relative;
  width: 80%;
  margin: 0 auto;
  .code-input {
    width: 54%;
    padding-right: 240rpx;
  }
  .get-code-btn {
    font-size: 30rpx;
    color: #504f4f;
    position: absolute;
    right: 0;
    top: 50%;
    transform: translateY(-50%);
    background: none;
    line-height: 1;
    padding: 20rpx;
    z-index: 2;
    border-left: #ff6a6a 1rpx solid;
    border-radius: 0;
    &::after {
      border: none;
    }
    &.disabled {
      color: #999;
    }
  }
}

.login-btn {
  width: 80%;
  height: 100rpx;
  line-height: 100rpx;
  border-radius: 50rpx;
  background: #FF6A6A;
  color: white;
  margin-top: 60rpx;
}

.forgot-password {
  text-align: right;
  padding: 0 12%;
  color: #00a6ff;
  font-size: 24rpx;
}
</style>
