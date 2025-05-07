<template>
	<view class="my-container">
		<!-- 顶部渐变背景 -->
		<view class="header-bg"></view>
		
		<!-- 用户信息卡片 -->
		<view class="user-card">
			<view class="user-info">
				<image class="avatar" :src="userInfo.avatar" mode="aspectFill"></image>
				<view class="info">
					<text class="nickname">{{userInfo.nickname}}</text>
					<text class="phone" v-if="userInfo.phone">{{userInfo.phone}}</text>
					<text class="user-id">ID: {{userInfo.userId}}</text>
				</view>
			</view>
		</view>
		
		<!-- 功能菜单 -->
		<view class="menu-list">
			<view class="menu-item" @click="goToPage('tongueRecord')">
				<image class="menu-icon" src="/static/tabbar/tongue.png" mode="aspectFit"></image>
				<text class="menu-text">舌诊记录</text>
				<text class="menu-arrow">&gt;</text>
			</view>
			<view class="menu-item" @click="goToPage('healthArchive')">
				<image class="menu-icon" src="/static/tabbar/tongue.png" mode="aspectFit"></image>
				<text class="menu-text">健康档案</text>
				<text class="menu-arrow">&gt;</text>
			</view>
			<view class="menu-item" @click="goToPage('shoppingCart')">
				<image class="menu-icon" src="/static/tabbar/tongue.png" mode="aspectFit"></image>
				<text class="menu-text">购物车</text>
				<text class="menu-arrow">&gt;</text>
			</view>
			<view class="menu-item" @click="goToPage('orderList')">
				<image class="menu-icon" src="/static/tabbar/tongue.png" mode="aspectFit"></image>
				<text class="menu-text">商品订单</text>
				<text class="menu-arrow">&gt;</text>
			</view>
		</view>
		
		<!-- 授权登录按钮 -->
		<button class="wx-btn" v-if="!isLogin" open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">微信一键登录</button>
	</view>
</template>

<script>
	// TODO: 需要修改为您的实际后端接口域名
	// 注意：域名必须在微信小程序管理后台的"服务器域名"中配置
	// 示例：https://api.example.com
	const BASE_URL = 'https://您的接口域名' 
	
	const APPID = 'wxf84e3c1545fc2a0f' 
	const SECRET = '43f4e58ab12e383bd864aaec431c7ef2' 
	const TOKEN_EXPIRE_TIME = 7 * 24 * 60 * 60 * 1000 
	
	export default {
		data() {
			return {
				isLogin: false,
				userInfo: {
					avatar: '/static/tabbar/tongue.png',
					nickname: '用户昵称',
					userId: '',
					phone: ''
				},
				loginData: {
					code: '',
					openid: '',
					session_key: '',
					phone: ''
				}
			}
		},
		onLoad() {
			// 在开发环境中允许请求任意域名（仅开发环境有效）
			// #ifdef MP-WEIXIN
			if(process.env.NODE_ENV === 'development') {
				wx.setStorageSync('wconsole', 'true')
				wx.setStorageSync('debugmock', 'true')
				wx.request({
					url: 'http://localhost:8080/debug',
					method: 'GET'
				})
			}
			// #endif
			
			this.checkLoginStatus()
		},
		methods: {
			// 检查登录状态
			checkLoginStatus() {
				const token = uni.getStorageSync('token')
				const loginTime = uni.getStorageSync('loginTime')
				const now = new Date().getTime()
				
				if (token && loginTime) {
					// 检查token是否过期
					if (now - loginTime < TOKEN_EXPIRE_TIME) {
						this.isLogin = true
						this.userInfo = uni.getStorageSync('userInfo')
					} else {
						// token过期，清除登录状态
						this.clearLoginStatus()
					}
				}
			},
			
			// 清除登录状态
			clearLoginStatus() {
				this.isLogin = false
				uni.removeStorageSync('token')
				uni.removeStorageSync('loginTime')
				uni.removeStorageSync('userInfo')
			},
			
			// 微信登录
			getPhoneNumber(e) {
				uni.login({
					provider: "weixin",
					onlyAuthorize: true, // 微信登录仅请求授权认证
					success: (event) => {
						const {code} = event
						//客户端成功获取授权临时票据（code）,向业务服务器发起登录请求。
						
						// 开发调试模式下模拟登录成功
						// #ifdef MP-WEIXIN
						if(process.env.NODE_ENV === 'development') {
							console.log('开发环境模拟登录成功，code:', code);
							
							// 模拟登录成功数据
							const mockUserData = {
								avatar: '/static/tabbar/tongue.png',
								nickname: '测试用户',
								userId: 'dev_user_123',
								phone: '1888888888',
								token: 'mock_token_' + Date.now()
							};
							
							// 更新界面显示
							this.userInfo = mockUserData;
							
							// 保存登录信息
							uni.setStorageSync('token', mockUserData.token);
							uni.setStorageSync('loginTime', new Date().getTime());
							uni.setStorageSync('userInfo', this.userInfo);
							
							this.isLogin = true;
							
							uni.showToast({
								title: '模拟登录成功',
								icon: 'success'
							});
							
							// 登录成功后跳转到首页
							setTimeout(() => {
								uni.switchTab({
									url: '/pages/index/index'
								});
							}, 1500);
							return;
						}
						// #endif
						
						uni.request({
							url: BASE_URL + '/api/user/login', //根据你的服务器地址修改
							data: {
								code: event.code
							},
							success: (res) => {
								if (res.data && res.data.code === 200) {
									const userData = res.data.data
									// 更新界面显示
									this.userInfo = {
										avatar: userData.avatar || '/static/tabbar/tongue.png',
										nickname: userData.nickname || '用户昵称',
										userId: userData.userId,
										phone: userData.phone
									}
									//获得token完成登录
									uni.setStorageSync('token', userData.token)
									uni.setStorageSync('loginTime', new Date().getTime())
									uni.setStorageSync('userInfo', this.userInfo)
									
									this.isLogin = true
									
									uni.showToast({
										title: '登录成功',
										icon: 'success'
									})
									
									// 登录成功后跳转到首页
									setTimeout(() => {
										uni.switchTab({
											url: '/pages/index/index'
										})
									}, 1500)
								} else {
									uni.showToast({
										title: res.data?.message || '登录失败',
										icon: 'none'
									})
								}
							},
							fail: (err) => {
								console.error('登录请求失败：', err)
								uni.showToast({
									title: '网络请求失败',
									icon: 'none'
								})
							}
						});
					},
					fail: (err) => {
						// 登录授权失败
						console.error('登录授权失败：', err)
						uni.showToast({
							title: '登录授权失败',
							icon: 'none'
						})
					}
				})
			},
			goToPage(page) {
				let url = '';
				switch(page) {
					case 'tongueRecord':
						url = '/pages/my/tongueRecord';
						break;
					case 'healthArchive':
						url = '/pages/my/healthArchive';
						break;
					case 'shoppingCart':
						url = '/pages/my/shoppingCart';
						break;
					case 'orderList':
						url = '/pages/my/orderList';
						break;
				}
				uni.navigateTo({
					url: url
				});
			}
		}
	}
</script>

<style lang="scss">
.my-container {
	min-height: 100vh;
	background-color: #f8f8f8;
	position: relative;
	
	.header-bg {
		position: relative;
		top: 10;
		left: 0;
		right: 0;
		height: 360rpx;
		background: linear-gradient(135deg, #00B391 0%, #43CD80 100%);
		border-bottom-left-radius: 40rpx;
        border-bottom-right-radius: 40rpx;
		z-index: 1;
	}
	
	.user-card {
		position: relative;
		top:50%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: 86%;
		background: #FFFFFF;
		border-radius: 20rpx;
		box-shadow: 0 10rpx 30rpx rgba(0, 0, 0, 0.05);
		padding: 40rpx;
		z-index: 2;
		
		.user-info {
			display: flex;
			align-items: center;
			
			.avatar {
				width: 120rpx;
				height: 120rpx;
				border-radius: 50%;
				border: 4rpx solid rgba(255, 255, 255, 0.8);
				background-color: #f8f8f8;
				box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.1);
			}
			
			.info {
				margin-left: 30rpx;
				
				.nickname {
					font-size: 36rpx;
					color: #333333;
					font-weight: 600;
					margin-bottom: 8rpx;
					display: block;
				}
				
				.phone {
					font-size: 28rpx;
					color: #666666;
					margin-bottom: 8rpx;
					display: block;
				}
				
				.user-id {
					font-size: 26rpx;
					color: rgba(0, 0, 0, 0.5);
				}
			}
		}
	}
	
	.menu-list {
		position: relative;
		z-index: 2;
		margin: 20rpx 40rpx;
		background: #FFFFFF;
		border-radius: 20rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
		overflow: hidden;
		
		.menu-item {
			display: flex;
			align-items: center;
			padding: 30rpx 40rpx;
			border-bottom: 1rpx solid #f5f5f5;
			
			&:last-child {
				border-bottom: none;
			}
			
			.menu-icon {
				width: 40rpx;
				height: 40rpx;
				margin-right: 20rpx;
			}
			
			.menu-text {
				flex: 1;
				font-size: 30rpx;
				color: #333333;
			}
			
			.menu-arrow {
				font-size: 28rpx;
				color: #999999;
			}
		}
	}
	
	.wx-btn {
		position: relative;
		z-index: 2;
		width: 670rpx;
		height: 88rpx;
		background: #00B391;
		border-radius: 44rpx;
		font-size: 32rpx;
		font-weight: 500;
		color: #FFFFFF;
		line-height: 88rpx;
		text-align: center;
		margin: 60rpx auto;
		box-shadow: 0 4rpx 12rpx rgba(0, 179, 145, 0.3);
		
		&:active {
			opacity: 0.9;
		}
	}
}
</style>