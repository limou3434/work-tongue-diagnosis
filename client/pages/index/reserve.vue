<template>
	<view class="appointment-container">
		<!-- 预约表单 -->
		<view class="appointment-form">
			<!-- 预约日期 -->
			<view class="form-item">
				<text class="form-label">预约日期</text>
				<picker 
					mode="date" 
					:value="formData.date" 
					:start="currentDate"
					@change="handleDateChange"
				>
					<view class="picker-value">{{formData.date || '请选择日期'}}</view>
				</picker>
			</view>
			
			<!-- 预约时间 -->
			<view class="form-item">
				<text class="form-label">预约时间</text>
				<picker :value="timeIndex" :range="timeSlots" @change="handleTimeChange">
					<view class="picker-value">{{timeSlots[timeIndex] || '请选择时间'}}</view>
				</picker>
			</view>
			
			<!-- 预约类型 -->
			<view class="form-item">
				<text class="form-label">预约类型</text>
				<picker :value="typeIndex" :range="appointmentTypes" @change="handleTypeChange">
					<view class="picker-value">{{appointmentTypes[typeIndex] || '请选择类型'}}</view>
				</picker>
			</view>
			
			<!-- 症状描述 -->
			<view class="form-item">
				<text class="form-label">症状描述</text>
				<textarea 
					class="symptom-textarea" 
					v-model="formData.symptoms" 
					placeholder="请描述您的症状（选填）"
					maxlength="200"
				></textarea>
				<text class="word-count">{{formData.symptoms.length}}/200</text>
			</view>
		</view>
		
		<!-- 提交按钮 -->
		<button class="submit-btn" @tap="submitAppointment">提交预约</button>
		
		<!-- 预约须知 -->
		<view class="appointment-notice">
			<text class="notice-title">预约须知</text>
			<text class="notice-item">1. 请提前至少1天预约</text>
			<text class="notice-item">2. 如需取消预约，请提前2小时联系客服</text>
			<text class="notice-item">3. 请准时到达，迟到超过15分钟将视为自动放弃</text>
			<text class="notice-item">4. 预约成功后，请保持电话畅通</text>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 预约表单数据
			formData: {
				date: '', // 预约日期
				time: '', // 预约时间
				type: '', // 预约类型
				symptoms: '' // 症状描述
			},
			// 预约类型列表
			appointmentTypes: ['儿科', '妇科', '综合诊断'],
			typeIndex: 0, // 当前选中的类型索引
			// 用户信息
			userId: 0,
			// 可预约时间列表（整点）
			timeSlots: [
				'09:00', '10:00', '11:00', '14:00', '15:00', '16:00', '17:00'
			],
			timeIndex: 0, // 当前选中的时间索引
			// 当前日期
			currentDate: ''
		}
	},
	onLoad() {
		// 获取用户信息
		this.getUserInfo()
		// 设置当前日期
		this.setCurrentDate()
	},
	methods: {
		// 设置当前日期
		setCurrentDate() {
			const date = new Date()
			const year = date.getFullYear()
			const month = (date.getMonth() + 1).toString().padStart(2, '0')
			const day = date.getDate().toString().padStart(2, '0')
			this.currentDate = `${year}-${month}-${day}`
		},
		// 获取用户信息
		getUserInfo() {
			uni.request({
				url: 'https://your-domain.com/work_tongue_diagnosis_api/user/get_info',
				method: 'GET',
				success: (res) => {
					if (res.statusCode === 200 && res.data.code === 0) {
						this.userId = res.data.data.userId
					}
				}
			})
		},
		// 日期选择
		handleDateChange(e) {
			this.formData.date = e.detail.value
		},
		// 时间选择
		handleTimeChange(e) {
			this.timeIndex = e.detail.value
			this.formData.time = this.timeSlots[this.timeIndex]
		},
		// 类型选择
		handleTypeChange(e) {
			this.typeIndex = e.detail.value
			this.formData.type = this.appointmentTypes[this.typeIndex]
		},
		// 提交预约
		submitAppointment() {
			// 表单验证
			if (!this.formData.date) {
				uni.showToast({
					title: '请选择预约日期',
					icon: 'none'
				})
				return
			}
			if (!this.formData.time) {
				uni.showToast({
					title: '请选择预约时间',
					icon: 'none'
				})
				return
			}
			
			// 构建提交数据
			const submitData = {
				userId: this.userId,
				appointmentDate: this.formData.date,
				appointmentTime: this.formData.time,
				appointmentType: this.formData.type,
				symptoms: this.formData.symptoms
			}
			
			// 发送预约请求
			uni.request({
				url: 'https://your-domain.com/work_tongue_diagnosis_api/appointment/create',
				method: 'POST',
				data: submitData,
				header: {
					'content-type': 'application/json'
				},
				success: (res) => {
					if (res.statusCode === 200 && res.data.code === 0) {
						uni.showToast({
							title: '预约成功',
							icon: 'success'
						})
						// 预约成功后跳转到预约记录页面
						setTimeout(() => {
							uni.navigateTo({
								url: '/pages/index/appointment-list'
							})
						}, 1500)
					} else {
						uni.showToast({
							title: res.data.message || '预约失败',
							icon: 'error'
						})
					}
				},
				fail: (err) => {
					uni.showToast({
						title: '网络错误',
						icon: 'error'
					})
					console.error('预约失败：', err)
				}
			})
		}
	}
}
</script>

<style lang="scss">
.appointment-container {
	min-height: 100vh;
	background-color: #f8f8f8;
	padding: 30rpx;
	
	.appointment-form {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 40rpx;
		margin-bottom: 40rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
		
		.form-item {
			margin-bottom: 40rpx;
			
			&:last-child {
				margin-bottom: 0;
			}
			
			.form-label {
				font-size: 32rpx;
				color: #333333;
				font-weight: 500;
				display: block;
				margin-bottom: 20rpx;
			}
			
			.picker-value {
				height: 88rpx;
				line-height: 88rpx;
				background-color: #f8f8f8;
				border-radius: 12rpx;
				padding: 0 30rpx;
				font-size: 28rpx;
				color: #333333;
				
				&:empty::before {
					content: '请选择';
					color: #999999;
				}
			}
			
			.symptom-textarea {
				width: 100%;
				height: 200rpx;
				background-color: #f8f8f8;
				border-radius: 12rpx;
				padding: 20rpx;
				font-size: 28rpx;
				color: #333333;
			}
			
			.word-count {
				font-size: 24rpx;
				color: #999999;
				text-align: right;
				display: block;
				margin-top: 10rpx;
			}
		}
	}
	
	.submit-btn {
		width: 100%;
		height: 88rpx;
		background-color: #00B391;
		color: #FFFFFF;
		font-size: 32rpx;
		border-radius: 44rpx;
		margin-bottom: 40rpx;
	}
	
	.appointment-notice {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 40rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
		
		.notice-title {
			font-size: 32rpx;
			color: #333333;
			font-weight: 600;
			display: block;
			margin-bottom: 20rpx;
		}
		
		.notice-item {
			font-size: 28rpx;
			color: #666666;
			line-height: 1.6;
			display: block;
			margin-bottom: 10rpx;
		}
	}
}
</style> 