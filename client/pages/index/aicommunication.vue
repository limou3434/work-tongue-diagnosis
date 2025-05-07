<template>
  <view class="page">
    <!-- 聊天区域 -->
    <scroll-view
      class="chat-scroll"
      scroll-y
      :scroll-into-view="lastMsgId"
      scroll-with-animation
    >
      <view
        v-for="(msg, index) in chatList"
        :key="index"
        :id="'msg-' + index"
        :class="['chat-item', msg.role]"
      >
        <image
          :src="msg.role === 'user' ? userAvatar : aiAvatar"
          class="avatar"
          mode="aspectFill"
        />
        <view class="bubble">
          <text>{{ msg.content }}</text>
        </view>
      </view>
    </scroll-view>

    <!-- 输入框 -->
    <view class="input-bar">
      <textarea
        class="input"
        v-model="inputMsg"
        auto-height
        maxlength="1000"
        placeholder="请输入内容..."
        @confirm="sendMessage"
      />
      <button class="send-btn" @click="sendMessage">发送</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      inputMsg: '',
      chatList: [],
      lastMsgId: '',
      userAvatar: '/static/tabbar/tongue.png', // ✅ 用户头像
      aiAvatar: '/static/tabbar/tongue.png'     // ✅ AI头像
    };
  },
  onLoad() {
    // ✅ 页面初始自动触发 AI 问候语
    this.chatList.push({
      role: 'ai',
      content: '你好，我是你的 AI 助手，有什么可以帮你的吗？'
    });
    this.scrollToBottom();
  },
  methods: {
    sendMessage() {
      const text = this.inputMsg.trim();
      if (!text) return;
      this.chatList.push({ role: 'user', content: text });
      this.inputMsg = '';
      this.scrollToBottom();

      // 后端请求
      uni.request({
        url: 'https://your-backend-api.com/api/chat', // ✅ 后端接口
        method: 'POST',
        data: { message: text },
        header: { 'Content-Type': 'application/json' },
        success: (res) => {
          const reply = res.data.reply || 'AI 响应失败';
          this.chatList.push({ role: 'ai', content: reply });
          this.scrollToBottom();
        },
        fail: () => {
          this.chatList.push({ role: 'ai', content: '❌ 请求失败，请稍后重试' });
        }
      });
    },
    scrollToBottom() {
      this.$nextTick(() => {
        this.lastMsgId = 'msg-' + (this.chatList.length - 1);
      });
    }
  }
};
</script>

<style scoped>
.page {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-color: #f2f3f5;
}

.chat-scroll {
  flex: 1;
  padding: 30rpx 20rpx;
  overflow: auto;
}

.chat-item {
  display: flex;
  align-items: flex-start;
  margin-bottom: 30rpx;
}

.chat-item.user {
  flex-direction: row-reverse;
}

.avatar {
  width: 64rpx;
  height: 64rpx;
  border-radius: 50%;
  margin: 0 20rpx;
}

.bubble {
  max-width: 70%;
  padding: 20rpx 28rpx;
  border-radius: 24rpx;
  font-size: 30rpx;
  line-height: 1.6;
  word-break: break-word;
  box-shadow: 0 4rpx 10rpx rgba(0, 0, 0, 0.05);
}

.chat-item.user .bubble {
  background-color: #4caf50;
  color: #fff;
  border-bottom-right-radius: 0;
}

.chat-item.ai .bubble {
  background-color: #ffffff;
  color: #333;
  border-bottom-left-radius: 0;
}

.input-bar {
  display: flex;
  align-items: center;
  padding: 20rpx;
  border-top: 1rpx solid #ddd;
  background-color: #fff;
}

.input {
  flex: 1;
  min-height: 80rpx;
  max-height: 200rpx;
  background-color: #f5f5f5;
  padding: 16rpx;
  border-radius: 20rpx;
  font-size: 28rpx;
  margin-right: 20rpx;
}

.send-btn {
  padding: 0 30rpx;
  height: 80rpx;
  background-color: #4caf50;
  color: #fff;
  font-size: 28rpx;
  border-radius: 20rpx;
}
</style>
