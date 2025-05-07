<template>
	<view class="found">
		<!-- 搜索栏 -->
		<uni-search-bar radius="100" placeholder="搜索舌诊记录" @confirm="search" @input="input" clearButton="auto" :bgColor="'#FFFFFF'"
		></uni-search-bar>
		
		<!-- 轮播图 -->
		<swiper class="swiper" circular :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
			<swiper-item v-for="(item, index) in swiperList" :key="index">
				<image :src="item.image" mode="aspectFill" class="swiper-image"></image>
			</swiper-item>
		</swiper>
		
		<!-- 功能框 -->
		<view class="function-box">
			<view class="function-item" v-for="(item, index) in functionList" :key="index" @click="navigateTo(item.path)">
				<image :src="item.icon" mode="aspectFit" class="function-icon"></image>
				<text class="function-text">{{item.name}}</text>
			</view>
		</view>
		
		<!-- 医生推荐部分 -->
		<view class="section-title">
			<view class="left-line"></view>
			<text class="title-text">医生推荐</text>
			<view class="right-line"></view>
		</view>
		<view class="doctor-list">
			<view class="doctor-item" v-for="(doctor, index) in doctors" :key="index" @click="goToDoctorDetail(doctor.id)">
				<image :src="doctor.avatar" class="doctor-avatar"></image>
				<view class="doctor-info">
					<view class="doctor-name">{{doctor.name}}</view>
					<view class="doctor-title">{{doctor.title}}</view>
					<view class="doctor-department">{{doctor.department}}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				searchText: '',
				// 轮播图数据
				swiperList: [
					{ image: '/static/tabbar/route1.jpg' },
					{ image: '/static/tabbar/route2.jpg' },
					{ image: '/static/tabbar/route3.jpg' }
				],
				// 功能列表
				functionList: [
					{ name: '舌诊', icon: '/static/tabbar/tongue.png', path: '/pages/index/tongue' },
					{ name: '体检', icon: '/static/tabbar/report.png', path: '/pages/index/question' },
					{ name: '预约', icon: '/static/tabbar/reservation.png', path: '/pages/index/reserve' }
				],
				// 医生列表
				doctors: [
					{
						id: '1',
						name: '张医生',
						title: '主任医师',
						department: '中医内科',
						avatar: '/static/tabbar/tongue.png'
					},
					{
						id: '2',
						name: '李医生',
						title: '副主任医师',
						department: '中医诊断科',
						avatar: '/static/tabbar/tongue.png'
					},
					{
						id: '3',
						name: '王医生',
						title: '主治医师',
						department: '中医全科',
						avatar: '/static/tabbar/tongue.png'
					}
				],
				// 接口加载状态
				loading: false
			}
		},
		onLoad() {
			// 页面加载时获取医生列表
			this.getDoctorList()
		},
		methods: {
			search(e) {
				console.log('搜索内容：', e.value)
			},
			input(e) {
				this.searchText = e.value
			},
			navigateTo(path) {
				uni.navigateTo({
					url: path
				})
			},
			// 获取医生列表
			async getDoctorList() {
				try {
					this.loading = true
					
					// 接口调用
					const res = await uni.request({
						url: '/api/doctors',
						method: 'GET'
					})
					
					// 如果接口调用成功且返回数据，则使用接口数据
					if (res.statusCode === 200 && res.data && res.data.length > 0) {
						this.doctors = res.data
					} else {
						// 接口调用失败或数据为空时，保留默认数据
						console.log('接口返回数据为空，使用默认数据')
					}
				} catch (e) {
					console.error('获取医生列表失败', e)
				} finally {
					this.loading = false
				}
			},
			// 跳转到医生详情页
			goToDoctorDetail(id) {
				console.log('进入医生详情页，医生ID:', id);
				uni.navigateTo({
					url: `/pages/index/doctordetail?id=${id}`
				});
			}
		}
	}
</script>

<style lang="scss">
.found {
	padding: 20rpx;
	background-color: #f0f5f1;
	
	:deep(.uni-searchbar) {
		background-color: #f0f5f1;
	}
	
	:deep(.uni-searchbar__box) {
		background-color: #FFFFFF;
	}
	
	// 轮播图样式
	.swiper {
		height: 300rpx;
		margin: 20rpx 0;
		border-radius: 12rpx;
		overflow: hidden;
		
		.swiper-image {
			width: 100%;
			height: 100%;
		}
	}
	
	// 功能框样式
	.function-box {
		display: flex;
		justify-content: space-between;
		padding: 20rpx 0;
		background-color: #FFFFFF;
		border-radius: 12rpx;
		margin-bottom: 20rpx;
		
		.function-item {
			display: flex;
			flex-direction: column;
			align-items: center;
			width: 25%;
			
			.function-icon {
				width: 80rpx;
				height: 80rpx;
				margin-bottom: 10rpx;
			}
			
			.function-text {
				font-size: 24rpx;
				color: #333;
			}
		}
	}
	
	// 医生列表样式
	.doctor-list {
		display: flex;
		flex-direction: column;
		margin-top: 20rpx;
	}
	
	.doctor-item {
		display: flex;
		align-items: center;
		background-color: #fff;
		padding: 20rpx;
		margin-bottom: 20rpx;
		border-radius: 10rpx;
		box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
	}
	
	.doctor-avatar {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50rpx;
		margin-right: 20rpx;
	}
	
	.doctor-info {
		flex: 1;
	}
	
	.doctor-name {
		font-size: 32rpx;
		font-weight: bold;
		color: #333;
	}
	
	.doctor-title {
		font-size: 26rpx;
		color: #666;
		margin-top: 6rpx;
	}
	
	.doctor-department {
		font-size: 24rpx;
		color: #999;
		margin-top: 6rpx;
	}
}
</style> 