<template>
	<view class="shop-container">
		<!-- 分类栏 -->
		<view class="category-bar">
			<view 
				class="category-item" 
				:class="{ active: currentCategory === index }"
				v-for="(item, index) in categories" 
				:key="index"
				@tap="switchCategory(index)"
			>
				<text>{{item.name}}</text>
			</view>
		</view>
		
		<!-- 商品列表 -->
		<view class="product-list">
			<view class="product-item" v-for="(item, index) in productList" :key="index" @click="goToDetail(item)">
				<image :src="item.image" mode="aspectFill" class="product-image"></image>
				<view class="product-info">
					<text class="product-name">{{item.name}}</text>
					<text class="product-desc">{{item.description}}</text>
					<view class="product-price">
						<text class="price">¥{{item.price}}</text>
						<text class="unit">/{{item.unit}}</text>
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
				currentCategory: 0,
				categories: [
					{ name: '药品' },
					{ name: '理疗' }
				],
				productList: [],
				loading: false,
				// 假数据
				mockData: {
					medicine: [
						{
							id: 1,
							name: '感冒灵颗粒',
							description: '用于感冒引起的头痛发热',
							price: 29.9,
							unit: '盒',
							image: '/static/tabbar/tongue.png'
						},
						{
							id: 2,
							name: '板蓝根颗粒',
							description: '清热解毒，凉血利咽',
							price: 25.8,
							unit: '盒',
							image: '/static/tabbar/tongue.png'
						},
						{
							id: 3,
							name: '银翘解毒片',
							description: '清热解毒，用于风热感冒',
							price: 32.5,
							unit: '盒',
							image: '/static/tabbar/tongue.png'
						},
						{
							id: 4,
							name: '感冒清热颗粒',
							description: '疏风散寒，解表清热',
							price: 28.6,
							unit: '盒',
							image: '/static/tabbar/tongue.png'
						}
					],
					therapy: [
						{
							id: 5,
							name: '艾灸套装',
							description: '传统艾灸理疗套装',
							price: 199,
							unit: '套',
							image: '/static/tabbar/tongue.png'
						},
						{
							id: 6,
							name: '拔罐器',
							description: '真空拔罐理疗器',
							price: 89,
							unit: '套',
							image: '/static/tabbar/tongue.png'
						},
						{
							id: 7,
							name: '按摩精油',
							description: '中医推拿按摩精油',
							price: 68,
							unit: '瓶',
							image: '/static/tabbar/tongue.png'
						},
						{
							id: 8,
							name: '理疗贴',
							description: '穴位理疗贴',
							price: 45,
							unit: '盒',
							image: '/static/tabbar/tongue.png'
						}
					]
				}
			}
		},
		onLoad() {
			this.getProductList()
		},
		methods: {
			// 切换分类
			switchCategory(index) {
				if (this.currentCategory === index) return
				this.currentCategory = index
				this.getProductList()
			},
			// 获取商品列表
			async getProductList() {
				try {
					this.loading = true
					// 使用假数据
					this.productList = this.currentCategory === 0 ? this.mockData.medicine : this.mockData.therapy
					
					// 接口调用逻辑（暂时注释）
					const res = await uni.request({
						url: `/api/shopping/found/${this.currentCategory}/`,
						method: 'POST',
						data: {
							// 这里可以添加其他参数，比如分页等
						}
					})
					
					if (res.statusCode === 200) {
						this.productList = res.data
					} else {
						uni.showToast({
							title: '获取商品列表失败',
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
			// 跳转到商品详情
			goToDetail(item) {
				console.log('点击商品:', item)
				uni.navigateTo({
					url: `/pages/shopping/detail?id=${item.id}`,
					success: () => {
						console.log('跳转成功')
					},
					fail: (err) => {
						console.log('跳转失败:', err)
					}
				})
			}
		}
	}
</script>

<style lang="scss">
.shop-container {
	min-height: 100vh;
	background-color: #f5f5f5;
	
	/* 分类栏样式 */
	.category-bar {
		display: flex;
		background-color: #ffffff;
		padding: 20rpx 0;
		position: sticky;
		top: 0;
		z-index: 100;
		
		.category-item {
			flex: 1;
			text-align: center;
			position: relative;
			padding: 20rpx 0;
			
			text {
				font-size: 28rpx;
				color: #333;
			}
			
			&.active {
				text {
					color: #43CD80;
					font-weight: bold;
				}
				
				&::after {
					content: '';
					position: absolute;
					bottom: 0;
					left: 50%;
					transform: translateX(-50%);
					width: 40rpx;
					height: 4rpx;
					background-color: #43CD80;
					border-radius: 2rpx;
				}
			}
		}
	}
	
	/* 商品列表样式 */
	.product-list {
		display: flex;
		flex-wrap: wrap;
		padding: 20rpx;
		justify-content: space-between;
		
		.product-item {
			width: 345rpx;
			background-color: #ffffff;
			border-radius: 12rpx;
			margin-bottom: 20rpx;
			overflow: hidden;
			
			.product-image {
				width: 100%;
				height: 345rpx;
			}
			
			.product-info {
				padding: 20rpx;
				
				.product-name {
					font-size: 28rpx;
					color: #333;
					font-weight: bold;
					margin-bottom: 10rpx;
				}
				
				.product-desc {
					font-size: 24rpx;
					color: #999;
					margin-bottom: 20rpx;
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
				}
				
				.product-price {
					display: flex;
					align-items: baseline;
					
					.price {
						font-size: 32rpx;
						color: #ff6b6b;
						font-weight: bold;
					}
					
					.unit {
						font-size: 24rpx;
						color: #999;
						margin-left: 4rpx;
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