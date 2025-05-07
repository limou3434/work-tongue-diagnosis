<template>
	<view class="tongue-container">
		<!-- 顶部标题区域 -->
		<view class="header">
			<text class="title">舌诊智能分析</text>
			<text class="subtitle">请保持光线充足，拍摄清晰的舌头照片</text>
		</view>
		
		<!-- 拍照/选择图片区域 -->
		<view class="upload-section" v-if="!tempFilePath">
			<view class="upload-box" @tap="chooseImage">
				<image class="upload-icon" src="/static/tabbar/tongue.png" mode="aspectFit"></image>
				<text class="upload-text">点击拍照或选择图片</text>
				<text class="upload-tip">建议在自然光下拍摄</text>
			</view>
		</view>
		
		<!-- 图片预览区域 -->
		<view class="preview-section" v-if="tempFilePath">
			<view class="preview-header">
				<text class="preview-title">图片预览</text>
				<text class="preview-action" @tap="chooseImage">重新拍摄</text>
			</view>
			<image :src="tempFilePath" mode="aspectFit" class="preview-image"></image>
			<button class="analyze-btn" @tap="analyzeImage" :disabled="analyzing">
				<text v-if="!analyzing">开始分析</text>
				<text v-else>分析中...</text>
			</button>
		</view>
		
		<!-- 分析结果区域 -->
		<view class="result-section" v-if="analysisResult">
			<view class="result-header">
				<text class="result-title">分析结果</text>
				<text class="result-time">{{currentTime}}</text>
			</view>
			
			<view class="result-card">
				<view class="result-item" v-for="(item, index) in analysisResult" :key="index">
					<text class="item-label">{{item.label}}</text>
					<text class="item-value">{{item.value}}</text>
				</view>
			</view>
			
			<view class="result-actions">
				<button class="action-btn save-btn" @tap="saveResult">保存结果</button>
				<button class="action-btn share-btn" @tap="shareResult">ai咨询</button>
			</view>
		</view>
		
		<!-- 使用说明 -->
		<view class="guide-section" v-if="!tempFilePath && !analysisResult">
			<view class="guide-title">拍摄指南</view>
			<view class="guide-items">
				<view class="guide-item">
					<text class="guide-dot">1</text>
					<text class="guide-text">请在光线充足的环境下拍摄</text>
				</view>
				<view class="guide-item">
					<text class="guide-dot">2</text>
					<text class="guide-text">保持舌头自然伸出，不要用力</text>
				</view>
				<view class="guide-item">
					<text class="guide-dot">3</text>
					<text class="guide-text">确保拍摄画面清晰无遮挡</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tempFilePath: '', // 临时图片路径
				analysisResult: null, // 分析结果
				analyzing: false, // 是否正在分析
				currentTime: '', // 当前时间
			}
		},
		methods: {
			// 选择图片
			chooseImage() {
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['camera', 'album'],
					success: (res) => {
						this.tempFilePath = res.tempFilePaths[0]
						this.analysisResult = null // 清除之前的分析结果
					},
					fail: (err) => {
						uni.showToast({
							title: '选择图片失败',
							icon: 'none'
						})
					}
				})
			},
			
			// 分析图片
			analyzeImage() {
				if (this.analyzing) return;
				
				this.analyzing = true;
				// 设置当前时间
				this.currentTime = new Date().toLocaleString();
				
				// 上传图片并获取分析结果
				uni.uploadFile({
					url: 'https://您的接口域名/api/analyze', // 替换为实际的后端接口
					filePath: this.tempFilePath,
					name: 'file',
					success: (uploadRes) => {
						const data = JSON.parse(uploadRes.data);
						if (data.code === 200) {
							this.analysisResult = data.result;
							uni.showToast({
								title: '分析成功',
								icon: 'success'
							});
						} else {
							uni.showToast({
								title: data.message || '分析失败',
								icon: 'none'
							});
						}
					},
					fail: (err) => {
						// 输出假数据
						this.analysisResult = [
							{ label: '舌质', value: '淡红舌' },
							{ label: '舌苔', value: '薄白苔' },
							{ label: '舌形', value: '正常' },
							{ label: '健康建议', value: '舌象基本正常，建议保持良好的作息习惯' }
						];
						uni.showToast({
							title: '使用假数据',
							icon: 'none'
						});
					},
					complete: () => {
						this.analyzing = false;
					}
				});
			},
			
			// 保存结果
			saveResult() {
				uni.showToast({
					title: '保存成功',
					icon: 'success'
				})
			},
			
			// ai咨询
			shareResult() {
				uni.navigateTo({
					url: '/pages/index/aicommunication'
				});
			}
		}
	}
</script>

<style lang="scss">
.tongue-container {
	min-height: 100vh;
	background-color: #f8f8f8;
	padding: 30rpx;
	
	.header {
		text-align: center;
		margin-bottom: 40rpx;
		
		.title {
			font-size: 36rpx;
			font-weight: 600;
			color: #333333;
			display: block;
			margin-bottom: 12rpx;
		}
		
		.subtitle {
			font-size: 26rpx;
			color: #666666;
		}
	}
	
	.upload-section {
		margin: 40rpx 0;
		
		.upload-box {
			background: #FFFFFF;
			border-radius: 20rpx;
			padding: 60rpx 40rpx;
			text-align: center;
			box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
			
			.upload-icon {
				width: 120rpx;
				height: 120rpx;
				margin-bottom: 20rpx;
			}
			
			.upload-text {
				font-size: 32rpx;
				color: #333333;
				display: block;
				margin-bottom: 12rpx;
			}
			
			.upload-tip {
				font-size: 24rpx;
				color: #999999;
			}
		}
	}
	
	.preview-section {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 30rpx;
		margin-bottom: 40rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
		
		.preview-header {
			display: flex;
			justify-content: space-between;
			align-items: center;
			margin-bottom: 20rpx;
			
			.preview-title {
				font-size: 32rpx;
				font-weight: 600;
				color: #333333;
			}
			
			.preview-action {
				font-size: 28rpx;
				color: #00B391;
			}
		}
		
		.preview-image {
			width: 100%;
			height: 400rpx;
			border-radius: 12rpx;
			margin-bottom: 30rpx;
		}
		
		.analyze-btn {
			width: 100%;
			height: 88rpx;
			background: #00B391;
			color: #FFFFFF;
			font-size: 32rpx;
			border-radius: 44rpx;
			
			&[disabled] {
				opacity: 0.6;
			}
		}
	}
	
	.result-section {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 30rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
		
		.result-header {
			display: flex;
			justify-content: space-between;
			align-items: center;
			margin-bottom: 30rpx;
			
			.result-title {
				font-size: 32rpx;
				font-weight: 600;
				color: #333333;
			}
			
			.result-time {
				font-size: 24rpx;
				color: #999999;
			}
		}
		
		.result-card {
			background: #f8f8f8;
			border-radius: 12rpx;
			padding: 20rpx;
			margin-bottom: 30rpx;
			
			.result-item {
				display: flex;
				margin-bottom: 20rpx;
				
				&:last-child {
					margin-bottom: 0;
				}
				
				.item-label {
					width: 160rpx;
					font-size: 28rpx;
					color: #666666;
				}
				
				.item-value {
					flex: 1;
					font-size: 28rpx;
					color: #333333;
				}
			}
		}
		
		.result-actions {
			display: flex;
			justify-content: space-between;
			gap: 20rpx;
			
			.action-btn {
				flex: 1;
				height: 80rpx;
				font-size: 28rpx;
				border-radius: 40rpx;
				
				&.save-btn {
					background: #00B391;
					color: #FFFFFF;
				}
				
				&.share-btn {
					background: #FFFFFF;
					color: #00B391;
					border: 2rpx solid #00B391;
				}
			}
		}
	}
	
	.guide-section {
		margin-top: 60rpx;
		
		.guide-title {
			font-size: 32rpx;
			font-weight: 600;
			color: #333333;
			margin-bottom: 30rpx;
		}
		
		.guide-items {
			background: #FFFFFF;
			border-radius: 20rpx;
			padding: 30rpx;
			box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
			
			.guide-item {
				display: flex;
				align-items: center;
				margin-bottom: 20rpx;
				
				&:last-child {
					margin-bottom: 0;
				}
				
				.guide-dot {
					width: 40rpx;
					height: 40rpx;
					background: #00B391;
					color: #FFFFFF;
					border-radius: 50%;
					font-size: 24rpx;
					display: flex;
					align-items: center;
					justify-content: center;
					margin-right: 20rpx;
				}
				
				.guide-text {
					font-size: 28rpx;
					color: #666666;
				}
			}
		}
	}
}
</style> 