<template>
	<view class="ai-doctor">
		<view class="chat-container">
			<view class="chat-messages">
				<view v-for="(message, index) in messages" :key="index" :class="['message', message.type === 'user' ? 'user-message' : 'ai-message']">
					<image :src="message.type === 'user' ? '/static/tabbar/tongue.png' : '/static/tabbar/tongue.png'" class="avatar" />
					<text>{{ message.text }}</text>
				</view>
			</view>
			<view class="chat-input">
				<input type="text" placeholder="请输入您的问题..." v-model="userInput" />
				<button @click="sendMessage">发送</button>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	name: 'newAIDoctor',
	data() {
		return {
			userInput: '',
			messages: [
				{ type: 'ai', text: '您好，我是智能医生，很高兴为您服务。请问您有什么不适吗？' }
			]
		}
	},
	methods: {
		sendMessage() {
			if (this.userInput.trim() !== '') {
				this.messages.push({ type: 'user', text: this.userInput });
				this.userInput = '';
				// 调用后端接口获取AI回复
				// 示例: axios.post('/api/ai/reply', { message: this.userInput })
				setTimeout(() => {
					this.messages.push({ type: 'ai', text: '这是AI的回复。' });
				}, 1000);
			}
		}
	}
}
</script>

<style scoped>
.ai-doctor {
	padding: 20rpx;
	background: #f0f5f1;
	height: 100vh;
	display: flex;
	flex-direction: column;
}

.chat-container {
	flex: 1;
	display: flex;
	flex-direction: column;
	justify-content: space-between;
	margin-top: 20rpx;
	background-color: #ffffff;
	border-radius: 10rpx;
	box-shadow: 0 4rpx 10rpx rgba(0, 0, 0, 0.1);
	padding: 20rpx;
}

.chat-messages {
	flex: 1;
	overflow-y: auto;
	margin-bottom: 20rpx;
	padding-right: 10rpx;
}

.message {
	display: flex;
	align-items: center;
	margin-bottom: 10rpx;
	padding: 10rpx;
	border-radius: 10rpx;
	max-width: 70%;
	box-shadow: 0 2rpx 5rpx rgba(0, 0, 0, 0.1);
	background-color: #ffffff;
	border: 1rpx solid #ddd;
}

.user-message {
	background-color: #e0f7fa;
	align-self: flex-end;
	color: #00796b;
	flex-direction: row-reverse;
	border: 1rpx solid #00B391;
}

.ai-message {
	background-color: #f1f8e9;
	align-self: flex-start;
	color: #33691e;
	border: 1rpx solid #00B391;
}

.avatar {
	width: 50rpx;
	height: 50rpx;
	border-radius: 50%;
	margin: 0 10rpx;
	border: 1rpx solid #ddd;
}

.chat-input {
	display: flex;
	align-items: center;
	background-color: #f9f9f9;
	border-radius: 10rpx;
	padding: 10rpx;
	box-shadow: 0 2rpx 5rpx rgba(0, 0, 0, 0.1);
	border: 1rpx solid #ddd;
}

input {
	flex: 1;
	padding: 10rpx;
	border: none;
	border-radius: 5rpx;
	margin-right: 10rpx;
	background-color: #ffffff;
	box-shadow: inset 0 1rpx 3rpx rgba(0, 0, 0, 0.1);
	font-size: 28rpx;
	border: 1rpx solid #ddd;
}

button {
	padding: 10rpx 20rpx;
	background-color: #00B391;
	color: white;
	border: none;
	border-radius: 5rpx;
	box-shadow: 0 2rpx 5rpx rgba(0, 0, 0, 0.2);
	transition: background-color 0.3s ease, transform 0.1s ease;
	border: 1rpx solid #00B391;
}

button:hover {
	background-color: #00897b;
	transform: scale(1.05);
}
</style> 