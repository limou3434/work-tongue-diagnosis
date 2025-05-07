<template>
	<view class="article-detail">
		<!-- 文章封面 -->
		<image :src="article.cover" mode="widthFix" class="article-cover"></image>
		
		<!-- 文章信息 -->
		<view class="article-info">
			<view class="article-title">{{article.title}}</view>
			<view class="article-meta">
				<text class="publish-time">{{article.publishTime}}</text>
				<text class="read-count">阅读 {{article.readCount}}</text>
			</view>
		</view>
		
		<!-- 文章内容 -->
		<view class="article-content">
			<rich-text :nodes="article.content"></rich-text>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			article: {
				id: 0,
				title: '',
				description: '',
				cover: '',
				publishTime: '',
				readCount: 0,
				content: ''
			}
		}
	},
	onLoad(options) {
		// 获取文章ID
		const articleId = options.id
		console.log('接收到的文章ID:', articleId)
		if (!articleId) {
			uni.showToast({
				title: '文章ID不能为空',
				icon: 'none'
			})
			setTimeout(() => {
				uni.navigateBack()
			}, 1500)
			return
		}
		// 获取文章详情
		this.getArticleDetail(articleId)
	},
	methods: {
		// 获取文章详情
		async getArticleDetail(id) {
			try {
				// 使用假数据
				const mockData = {
					1: {
						id: 1,
						title: '中医舌诊的基础知识',
						description: '舌诊是中医诊断的重要方法之一，通过观察舌头的颜色、形态、苔质等特征来判断疾病...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-20',
						readCount: 1234,
						content: `
							<div style="padding: 30rpx;">
								<h3 style="font-size: 32rpx; font-weight: bold; margin-bottom: 20rpx;">什么是舌诊？</h3>
								<p style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">舌诊是中医诊断的重要方法之一，通过观察舌头的颜色、形态、苔质等特征来判断疾病。舌诊具有悠久的历史，是中医"望闻问切"四诊中的重要组成部分。</p>
								
								<h3 style="font-size: 32rpx; font-weight: bold; margin-bottom: 20rpx;">舌诊的基本内容</h3>
								<p style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">舌诊主要包括以下几个方面：</p>
								<ul style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">
									<li>舌色：正常舌色为淡红色，异常可出现淡白、红绛、紫暗等</li>
									<li>舌形：包括舌体大小、厚薄、胖瘦等</li>
									<li>舌苔：观察苔色、苔质、苔的厚薄等</li>
								</ul>
								
								<h3 style="font-size: 32rpx; font-weight: bold; margin-bottom: 20rpx;">舌诊的意义</h3>
								<p style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">舌诊能够反映人体的整体状况，对于疾病的诊断和治疗具有重要的指导意义。通过舌诊可以：</p>
								<ul style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">
									<li>判断病位：不同部位的舌象反映不同的脏腑功能</li>
									<li>判断病性：通过舌象可以判断疾病的寒热虚实</li>
									<li>判断预后：舌象的变化可以反映疾病的发展趋势</li>
								</ul>
							</div>
						`
					},
					2: {
						id: 2,
						title: '常见舌象与疾病的关系',
						description: '不同的舌象往往反映不同的身体状况，本文将详细介绍常见舌象及其对应的疾病特征...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-19',
						readCount: 986,
						content: `
							<div style="padding: 30rpx;">
								<h3 style="font-size: 32rpx; font-weight: bold; margin-bottom: 20rpx;">常见舌象类型</h3>
								<p style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">在临床实践中，常见的舌象类型包括：</p>
								<ul style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">
									<li>淡白舌：多见于气血两虚、阳虚等证</li>
									<li>红绛舌：多见于阴虚火旺、热证等</li>
									<li>紫暗舌：多见于血瘀、寒凝等证</li>
								</ul>
								
								<h3 style="font-size: 32rpx; font-weight: bold; margin-bottom: 20rpx;">舌象与疾病的关系</h3>
								<p style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">不同的舌象往往反映不同的身体状况：</p>
								<ul style="font-size: 28rpx; line-height: 1.6; color: #333; margin-bottom: 20rpx;">
									<li>淡白舌：常见于贫血、营养不良等</li>
									<li>红绛舌：常见于发热、炎症等</li>
									<li>紫暗舌：常见于心血管疾病、血液循环障碍等</li>
								</ul>
							</div>
						`
					}
				}
				
				if (mockData[id]) {
					this.article = mockData[id]
				} else {
					// 接口调用逻辑（暂时注释）
					const res = await uni.request({
						url: `/api/found/articles/${id}/`,
						method: 'GET'
					})
					
					if (res.statusCode === 200) {
						this.article = res.data
					} else {
						uni.showToast({
							title: '获取文章详情失败',
							icon: 'none'
						})
					}
				}
			} catch (e) {
				uni.showToast({
					title: '网络请求失败',
					icon: 'none'
				})
			}
		}
	}
}
</script>

<style lang="scss">
.article-detail {
	min-height: 100vh;
	background-color: #ffffff;
	
	.article-cover {
		width: 100%;
	}
	
	.article-info {
		padding: 30rpx;
		border-bottom: 1rpx solid #f5f5f5;
		
		.article-title {
			font-size: 36rpx;
			font-weight: bold;
			color: #333;
			margin-bottom: 20rpx;
		}
		
		.article-meta {
			display: flex;
			justify-content: space-between;
			align-items: center;
			
			.publish-time, .read-count {
				font-size: 24rpx;
				color: #999;
			}
		}
	}
	
	.article-content {
		padding: 30rpx;
		
		:deep(h3) {
			font-size: 32rpx;
			font-weight: bold;
			color: #333;
			margin: 30rpx 0 20rpx;
		}
		
		:deep(p) {
			font-size: 28rpx;
			line-height: 1.6;
			color: #333;
			margin-bottom: 20rpx;
		}
		
		:deep(ul) {
			font-size: 28rpx;
			line-height: 1.6;
			color: #333;
			margin-bottom: 20rpx;
			padding-left: 30rpx;
		}
	}
}
</style> 