<template>
	<view class="record-container">
		<!-- 文章列表 -->
		<view class="article-list">
			<view class="article-item" v-for="(item, index) in articleList" :key="index" @tap="goToDetail(item)">
				<image :src="item.cover" mode="aspectFill" class="article-image"></image>
				<view class="article-info">
					<text class="article-title">{{item.title}}</text>
					<text class="article-desc">{{item.description}}</text>
					<view class="article-meta">
						<text class="publish-time">{{item.publishTime}}</text>
						<text class="read-count">阅读 {{item.readCount}}</text>
					</view>
				</view>
			</view>
		</view>
		
		<!-- 加载更多 -->
		<view class="loading" v-if="loading">
			<text>加载中...</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loading: false,
				articleList: [
					{
						id: 1,
						title: '中医舌诊的基础知识',
						description: '舌诊是中医诊断的重要方法之一，通过观察舌头的颜色、形态、苔质等特征来判断疾病...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-20',
						readCount: 1234
					},
					{
						id: 2,
						title: '常见舌象与疾病的关系',
						description: '不同的舌象往往反映不同的身体状况，本文将详细介绍常见舌象及其对应的疾病特征...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-19',
						readCount: 986
					},
					{
						id: 3,
						title: '舌诊在中医养生中的应用',
						description: '如何通过舌诊来指导日常养生？本文将从中医理论出发，为您详细讲解舌诊在养生保健中的应用...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-18',
						readCount: 756
					},
					{
						id: 4,
						title: '现代医学对舌诊的研究进展',
						description: '随着科技的发展，舌诊也在与时俱进。本文将介绍现代医学对舌诊的研究成果和应用...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-17',
						readCount: 543
					},
					{
						id: 5,
						title: '现代医学对舌诊的研究进展',
						description: '随着科技的发展，舌诊也在与时俱进。本文将介绍现代医学对舌诊的研究成果和应用...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-17',
						readCount: 543
					},
					{
						id: 6,
						title: '现代医学对舌诊的研究进展',
						description: '随着科技的发展，舌诊也在与时俱进。本文将介绍现代医学对舌诊的研究成果和应用...',
						cover: '/static/tabbar/tongue.png',
						publishTime: '2024-03-17',
						readCount: 543
					}
				]
			}
		},
		onLoad() {
			this.getArticleList()
		},
		methods: {
			// 获取文章列表
			async getArticleList() {
				try {
					this.loading = true
					// 这里可以添加接口调用
					const res = await uni.request({
						url: '/api/found/articles',
						method: 'GET'
					})
					
					if (res.statusCode === 200) {
						this.articleList = res.data
					} else {
						uni.showToast({
							title: '获取文章列表失败',
							icon: 'none'
						})
					}

				} catch (e) {
					uni.showToast({
						title: '网络请求失败',
						icon: 'none'
					})
				} finally {
					this.loading = false
				}
			},
			// 跳转到文章详情
			goToDetail(item) {
				uni.navigateTo({
					url: `/pages/found/detail?id=${item.id}`
				})
			}
		}
	}
</script>

<style lang="scss">
.record-container {
	min-height: 100vh;
	background-color: #f5f5f5;
	padding: 20rpx;
	
	/* 文章列表样式 */
	.article-list {
		.article-item {
			background-color: #ffffff;
			border-radius: 12rpx;
			margin-bottom: 20rpx;
			overflow: hidden;
			display: flex;
			padding: 30rpx;
			
			.article-image {
				width: 220rpx;
				height: 200rpx;
				border-radius: 8rpx;
				margin-right: 20rpx;
			}
			
			.article-info {
				flex: 1;
				display: flex;
				flex-direction: column;
				justify-content: space-between;
				
				.article-title {
					font-size: 30rpx;
					color: #333;
					font-weight: bold;
					margin-bottom: 10rpx;
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
				}
				
				.article-desc {
					font-size: 26rpx;
					color: #666;
					margin-bottom: 10rpx;
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
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
		}
	}
	
	/* 加载更多样式 */
	.loading {
		text-align: center;
		padding: 20rpx 0;
		
		text {
			font-size: 24rpx;
			color: #999;
		}
	}
}
</style> 