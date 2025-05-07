<template>
	<view>
		<page-head :title="title"></page-head>
		<view class="uni-common-pb">
			<view class="uni-padding-wrap">
				<view class="uni-container">
					<slot></slot>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	page {
		min-height: 100%;
		background-color: #f5f5f5;
	}
	
	/* 状态栏高度 */
	.status_bar {
		height: var(--status-bar-height);
		width: 100%;
	}
	
	/* 导航栏高度 */
	.nav_bar {
		height: 44px;
		width: 100%;
	}
	
	/* 顶部导航 */
	.nav {
		width: 100%;
		background-color: #43CD80;
	}
	
	.uni-container {
		padding: 15px;
		flex: 1;
	}
	
	.uni-padding-wrap {
		width: 690rpx;
		padding: 0 30rpx;
	}
	
	.uni-common-pb {
		padding-bottom: 30rpx;
	}
</style>
