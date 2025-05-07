<template>
	<view class="container" style="padding: 20rpx; background-color: #f5f5f5; min-height: 100vh;">
		<!-- 医生基本信息 -->
		<view class="doctor-card" style="background-color: #ffffff; border-radius: 20rpx; padding: 30rpx; margin-bottom: 30rpx; box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);">
			<view style="display: flex; flex-direction: column; align-items: center; margin-bottom: 30rpx;">
				<image :src="avatar" mode="aspectFill" style="width: 180rpx; height: 180rpx; border-radius: 90rpx; margin-bottom: 20rpx;"></image>
				<view style="font-size: 36rpx; font-weight: bold; color: #333;">{{name}}</view>
				<view style="font-size: 28rpx; color: #666; margin-top: 10rpx;">{{title}}</view>
				<view style="font-size: 28rpx; color: #666; margin-top: 10rpx;">{{department}}</view>
			</view>
		</view>
		
		<!-- 专业特长 -->
		<view class="section" style="background-color: #ffffff; border-radius: 20rpx; padding: 30rpx; margin-bottom: 30rpx; box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);">
			<view style="font-size: 32rpx; font-weight: bold; color: #333; border-left: 8rpx solid #00B391; padding-left: 20rpx; margin-bottom: 20rpx;">专业特长</view>
			<view style="font-size: 28rpx; color: #666; line-height: 1.6;">{{specialty}}</view>
		</view>
		
		<!-- 个人简介 -->
		<view class="section" style="background-color: #ffffff; border-radius: 20rpx; padding: 30rpx; margin-bottom: 30rpx; box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);">
			<view style="font-size: 32rpx; font-weight: bold; color: #333; border-left: 8rpx solid #00B391; padding-left: 20rpx; margin-bottom: 20rpx;">个人简介</view>
			<view style="font-size: 28rpx; color: #666; line-height: 1.6;">{{introduction}}</view>
		</view>
		
		<!-- 预约按钮 -->
		<view style="padding: 40rpx 0;">
			<button @click="makeReservation" style="background: linear-gradient(to right, #00B391, #43CD80); color: #ffffff; border-radius: 50rpx; padding: 25rpx 0; font-size: 32rpx; width: 90%; margin: 0 auto; border: none;">立即预约</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				doctorId: '',
				avatar: '',
				name: '',
				title: '',
				department: '',
				specialty: '',
				introduction: '',
				// 示例医生数据
				doctorsData: [
					{
						id: '1',
						avatar: '/static/tabbar/tongue.png',
						name: '张医生',
						title: '主任医师',
						department: '中医内科',
						specialty: '擅长各类常见疾病的中医诊断与治疗，尤其在消化系统疾病、亚健康调理、舌诊方面有丰富的临床经验。对舌诊在中医辨证中的应用有独特见解。',
						introduction: '张医生从医15年，毕业于北京中医药大学，曾在多家三甲医院任职。多年来致力于中医舌诊的研究与临床应用，发表多篇相关学术论文。温和耐心，深受患者信赖，注重医患沟通，坚持个性化诊疗方案的制定。'
					},
					{
						id: '2',
						avatar: '/static/tabbar/tongue.png',
						name: '李医生',
						title: '副主任医师',
						department: '中医诊断科',
						specialty: '专注于舌诊、脉诊等中医诊断技术的应用与研究，对舌象变化与内脏功能的关系有深入研究。擅长通过舌诊辅助诊断心脑血管疾病、消化系统疾病等。',
						introduction: '李医生毕业于上海中医药大学，师从著名中医诊断学专家。从事中医诊断教学与临床工作12年，主持多项舌诊相关研究课题，开发了多种舌诊辅助诊断方法。诊疗风格严谨细致，善于发现疾病早期信号。'
					},
					{
						id: '3',
						avatar: '/static/tabbar/tongue.png',
						name: '王医生',
						title: '主治医师',
						department: '中医全科',
						specialty: '擅长运用舌诊、面诊等中医诊断方法进行综合分析，尤其专注于亚健康调理、慢性疾病管理和中医养生保健指导。对舌诊与现代医学结合有独到见解。',
						introduction: '王医生毕业于广州中医药大学，具有扎实的中医理论基础和丰富的临床实践经验。积极探索传统中医与现代技术的结合，参与多项中医诊断数字化研究。治疗注重整体观念，擅长制定个性化健康管理方案。'
					}
				]
			}
		},
		onLoad(options) {
			console.log('Doctor detail onLoad, options:', options);
			if (options && options.id) {
				this.doctorId = options.id;
				this.loadDoctorInfo(this.doctorId);
			} else {
				uni.showToast({
					title: '医生ID不存在',
					icon: 'none'
				});
				setTimeout(() => {
					uni.navigateBack();
				}, 1500);
			}
		},
		methods: {
			// 加载医生信息
			loadDoctorInfo(id) {
				console.log('Loading doctor info for ID:', id);
				const doctor = this.doctorsData.find(item => item.id === id);
				
				if (doctor) {
					this.avatar = doctor.avatar;
					this.name = doctor.name;
					this.title = doctor.title;
					this.department = doctor.department;
					this.experienceYears = doctor.experienceYears;
					this.patients = doctor.patients;
					this.rating = doctor.rating;
					this.specialty = doctor.specialty;
					this.introduction = doctor.introduction;
					console.log('Doctor info loaded successfully');
				} else {
					console.log('Doctor not found');
					uni.showToast({
						title: '未找到医生信息',
						icon: 'none'
					});
					setTimeout(() => {
						uni.navigateBack();
					}, 1500);
				}
			},
			// 预约功能
			makeReservation() {
				uni.navigateTo({
					url: `/pages/index/reserve?doctorId=${this.doctorId}&doctorName=${this.name}`
				});
			}
		}
	}
</script> 