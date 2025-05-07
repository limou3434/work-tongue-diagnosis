<template>
	<view class="detail-container">
		<!-- 商品图片 -->
		<image :src="product.image" mode="widthFix" class="product-image"></image>
		
		<!-- 商品信息 -->
		<view class="product-info">
			<view class="product-name">{{product.name}}</view>
			<view class="product-desc">{{product.description}}</view>
			<view class="product-price">
				<text class="price">¥{{product.price}}</text>
				<text class="unit">/{{product.unit}}</text>
			</view>
		</view>
		
		<!-- 商品详情 -->
		<view class="product-detail">
			<view class="detail-title">商品详情</view>
			<rich-text :nodes="product.detail"></rich-text>
		</view>
		
		<!-- 底部操作栏 -->
		<view class="bottom-bar">
			<view class="quantity-control">
				<text class="minus" @tap="decreaseQuantity">-</text>
				<text class="quantity">{{quantity}}</text>
				<text class="plus" @tap="increaseQuantity">+</text>
			</view>
			<button class="add-to-cart" @tap="addToCart">加入购物车</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			product: {
				id: 0,
				name: '',
				description: '',
				price: 0,
				unit: '',
				image: '',
				detail: ''
			},
			quantity: 1
		}
	},
	onLoad(options) {
		// 获取商品ID
		const productId = options.id
		console.log('接收到的商品ID:', productId)
		if (!productId) {
			uni.showToast({
				title: '商品ID不能为空',
				icon: 'none'
			})
			setTimeout(() => {
				uni.navigateBack()
			}, 1500)
			return
		}
		// 获取商品详情
		this.getProductDetail(productId)
	},
	methods: {
		// 获取商品详情
		async getProductDetail(id) {
			try {
				// 使用假数据
				const mockData = {
					1: {
						id: 1,
						name: '感冒灵颗粒',
						description: '用于感冒引起的头痛发热',
						price: 29.9,
						unit: '盒',
						image: '/static/tabbar/tongue.png',
						detail: '<div style="padding: 20rpx;">感冒灵颗粒是一种中成药，主要用于治疗感冒引起的头痛、发热、鼻塞、流涕等症状。本品为棕黄色颗粒，味甜、微苦。</div>'
					},
					2: {
						id: 2,
						name: '板蓝根颗粒',
						description: '清热解毒，凉血利咽',
						price: 25.8,
						unit: '盒',
						image: '/static/tabbar/tongue.png',
						detail: '<div style="padding: 20rpx;">板蓝根颗粒是一种中成药，具有清热解毒、凉血利咽的功效。主要用于治疗风热感冒、咽喉肿痛等症状。</div>'
					}
				}
				
				if (mockData[id]) {
					this.product = mockData[id]
				} else {
					// 接口调用逻辑（暂时注释）
					const res = await uni.request({
						url: `/api/shopping/detail/${id}/`,
						method: 'GET'
					})
					
					if (res.statusCode === 200) {
						this.product = res.data
					} else {
						uni.showToast({
							title: '获取商品详情失败',
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
		},
		// 减少数量
		decreaseQuantity() {
			if (this.quantity > 1) {
				this.quantity--
			}
		},
		// 增加数量
		increaseQuantity() {
			this.quantity++
		},
		// 加入购物车
		addToCart() {
			uni.showToast({
				title: '已加入购物车',
				icon: 'success'
			})
		}
	}
}
</script>

<style lang="scss">
.detail-container {
	min-height: 100vh;
	background-color: #f5f5f5;
	padding-bottom: 120rpx;
	
	.product-image {
		width: 100%;
	}
	
	.product-info {
		background-color: #ffffff;
		padding: 30rpx;
		margin-bottom: 20rpx;
		
		.product-name {
			font-size: 32rpx;
			font-weight: bold;
			color: #333;
			margin-bottom: 20rpx;
		}
		
		.product-desc {
			font-size: 28rpx;
			color: #666;
			margin-bottom: 30rpx;
		}
		
		.product-price {
			display: flex;
			align-items: baseline;
			
			.price {
				font-size: 36rpx;
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
	
	.product-detail {
		background-color: #ffffff;
		padding: 30rpx;
		
		.detail-title {
			font-size: 32rpx;
			font-weight: bold;
			color: #333;
			margin-bottom: 20rpx;
		}
	}
	
	.bottom-bar {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		height: 100rpx;
		background-color: #ffffff;
		display: flex;
		align-items: center;
		padding: 0 30rpx;
		box-shadow: 0 -2rpx 10rpx rgba(0, 0, 0, 0.05);
		
		.quantity-control {
			display: flex;
			align-items: center;
			margin-right: 30rpx;
			
			.minus, .plus {
				width: 60rpx;
				height: 60rpx;
				line-height: 60rpx;
				text-align: center;
				background-color: #f5f5f5;
				border-radius: 50%;
				font-size: 32rpx;
			}
			
			.quantity {
				width: 80rpx;
				text-align: center;
				font-size: 28rpx;
			}
		}
		
		.add-to-cart {
			flex: 1;
			height: 80rpx;
			line-height: 80rpx;
			text-align: center;
			background-color: #43CD80;
			color: #ffffff;
			border-radius: 40rpx;
			font-size: 28rpx;
		}
	}
}
</style> 