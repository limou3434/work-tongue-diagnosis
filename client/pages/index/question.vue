<template>
	<view class="question-container">
		<!-- 免责申明弹窗 -->
		<view class="disclaimer-modal" v-if="!hasStarted">
			<view class="disclaimer-content">
				<view class="disclaimer-header">
					<text class="disclaimer-title">免责申明</text>
				</view>
				<view class="disclaimer-body">
					<text class="disclaimer-text">1. 本问卷结果仅供参考，不能替代专业医疗诊断</text>
					<text class="disclaimer-text">2. 请如实填写问卷内容，以确保结果的准确性</text>
					<text class="disclaimer-text">3. 您的个人信息将被严格保密</text>
					<text class="disclaimer-text">4. 如遇身体不适，请及时就医</text>
				</view>
				<view class="disclaimer-footer">
					<button class="start-btn" @click="startQuestionnaire">开始测试</button>
				</view>
			</view>
		</view>

		<!-- 问卷内容 -->
		<view v-else>
			<!-- 顶部进度条 -->
			<view class="progress-bar">
				<view class="progress-inner" :style="{width: progress + '%'}"></view>
				<text class="progress-text">{{currentIndex + 1}}/{{questions.length}}</text>
			</view>
			<!-- 问题卡片 -->
			<view class="question-card" v-if="currentQuestion">
				<view class="question-header">
					<text class="question-title">{{currentQuestion.title}}</text>
					<text class="question-subtitle" v-if="currentQuestion.subtitle">{{currentQuestion.subtitle}}</text>
					<text class="question-note" v-if="currentQuestion.note">{{currentQuestion.note}}</text>
				</view>
				
				<!-- 单选问题 -->
				<view class="options-list" v-if="currentQuestion.type === 'radio'">
					<view 
						class="option-item" 
						v-for="(option, index) in currentQuestion.options" 
						:key="index"
						:class="{'selected': currentAnswer === option.value}"
						@click="selectOption(option.value)"
					>
						<view class="option-radio">
							<view class="radio-inner" v-if="currentAnswer === option.value"></view>
						</view>
						<text class="option-text">{{option.label}}</text>
					</view>
				</view> 
				
				<!-- 多选问题 -->
				<view class="options-list" v-if="currentQuestion.type === 'checkbox'">
					<view 
						class="option-item" 
						v-for="(option, index) in currentQuestion.options" 
						:key="index"
						:class="{'selected': currentAnswers.includes(option.value)}"
						@click="toggleOption(option.value)"
					>
						<view class="option-checkbox">
							<view class="checkbox-inner" v-if="currentAnswers.includes(option.value)"></view>
						</view>
						<text class="option-text">{{option.label}}</text>
					</view>
				</view>
			</view>

			<!-- 底部按钮 -->
			<view class="action-buttons">
				<button 
					class="action-btn prev-btn" 
					@click="prevQuestion" 
					:disabled="currentIndex === 0"
				>上一题</button>
				<button 
					class="action-btn next-btn" 
					@click="nextQuestion" 
					:disabled="!canProceed"
				>{{isLastQuestion ? '完成' : '下一题'}}</button>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			hasStarted: false, // 是否已开始问卷
			currentIndex: 0, // 当前问题索引
			currentAnswer: '', // 当前单选答案
			currentAnswers: [], // 当前多选答案
			questions: [], // 问卷问题列表
			questionnaireId: 0, // 问卷ID
			userId: 0, // 用户ID
			answersMap: {} // 存储所有问题的答案
		}
	},
	onLoad() {
		// 页面加载时获取问卷问题
		this.getQuestionnaireQuestions()
	},
	computed: {
		// 当前问题
		currentQuestion() {
			return this.questions[this.currentIndex]
		},
		// 进度百分比
		progress() {
			return ((this.currentIndex + 1) / this.questions.length) * 100
		},
		// 是否是最后一题
		isLastQuestion() {
			return this.currentIndex === this.questions.length - 1
		},
		// 是否可以进入下一题
		canProceed() {
			if (this.currentQuestion.type === 'radio') {
				return this.currentAnswer !== ''
			} else {
				return this.currentAnswers.length > 0
			}
		}
	},
	methods: {
		// 开始问卷
		startQuestionnaire() {
			this.hasStarted = true
		},
		// 选择单选答案
		selectOption(value) {
			this.answersMap[this.currentQuestion.id] = value
			this.currentAnswer = value
		},
		// 切换多选答案
		toggleOption(value) {
			let answerList = this.answersMap[this.currentQuestion.id] || []
			const index = answerList.indexOf(value)
			if (index === -1) {
				answerList.push(value)
			} else {
				answerList.splice(index, 1)
			}
			this.answersMap[this.currentQuestion.id] = answerList
			this.currentAnswers = [...answerList]
		},
		// 上一题
		prevQuestion() {
			if (this.currentIndex > 0) {
				this.currentIndex--
				this.resetCurrentAnswer()
			}
		},
		// 下一题
		nextQuestion() {
			if (this.currentIndex < this.questions.length - 1) {
				this.currentIndex++
				this.resetCurrentAnswer()
			} else {
				this.submitQuestionnaire()
			}
		},
		// 重置当前答案
		resetCurrentAnswer() {
			const qid = this.currentQuestion.id
			if (this.currentQuestion.type === 'radio') {
				this.currentAnswer = this.answersMap[qid] || ''
			} else {
				this.currentAnswers = this.answersMap[qid] || []
			}
		},
		// 获取问卷问题
		getQuestionnaireQuestions() {
			uni.request({
				url: 'https://your-domain.com/work_tongue_diagnosis_api/check/get_questionnaire',
				method: 'GET',
				success: (res) => {
					if (res.statusCode === 200 && res.data.code === 0) {
						this.questions = res.data.data.questions
						this.questionnaireId = res.data.data.questionnaireId
						this.userId = res.data.data.userId
					} else {
						uni.showToast({
							title: '获取问卷失败',
							icon: 'error'
						})
					}
				},
				fail: (err) => {
					uni.showToast({
						title: '网络错误',
						icon: 'error'
					})
					console.error('获取问卷失败：', err)
				}
			})
		},
		// 提交问卷
		submitQuestionnaire() {
			// 构建提交数据
			const submitData = {
				userId: this.userId,
				questionnaireId: this.questionnaireId,
				answers: this.questions.map((question) => ({
					questionId: question.id,
					selectedOption: question.type === 'radio' 
						? this.answersMap[question.id] 
						: (this.answersMap[question.id] || []).join(',')
				}))
			}
			
			// 发送POST请求
			uni.request({
				url: 'https://your-domain.com/work_tongue_diagnosis_api/check/questionnaire_diagnosis',
				method: 'POST',
				data: submitData,
				header: {
					'content-type': 'application/json'
				},
				success: (res) => {
					if (res.statusCode === 200 && res.data.code === 0) {
						uni.showToast({
							title: '提交成功',
							icon: 'success'
						})
						// 可以在这里处理返回的结果
						console.log('诊断结果：', res.data.data)
					} else {
						uni.showToast({
							title: res.data.message || '提交失败',
							icon: 'error'
						})
						console.error('提交失败：', res)
					}
				},
				fail: (err) => {
					uni.showToast({
						title: '网络错误',
						icon: 'error'
					})
					console.error('提交失败：', err)
				}
			})
		}
	}
}
</script>

<style lang="scss">
.question-container {
	min-height: 100vh;
	background-color: #f8f8f8;
	padding: 30rpx;
	
	.disclaimer-modal {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: rgba(0, 0, 0, 0.5);
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 999;
		
		.disclaimer-content {
			width: 600rpx;
			background-color: #FFFFFF;
			border-radius: 20rpx;
			padding: 40rpx;
			box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.1);
			
			.disclaimer-header {
				margin-bottom: 30rpx;
				
				.disclaimer-title {
					font-size: 36rpx;
					font-weight: 600;
					color: #333333;
					text-align: center;
					display: block;
				}
			}
			
			.disclaimer-body {
				margin-bottom: 40rpx;
				
				.disclaimer-text {
					font-size: 28rpx;
					color: #666666;
					line-height: 1.6;
					display: block;
					margin-bottom: 20rpx;
				}
			}
			
			.disclaimer-footer {
				.start-btn {
					width: 100%;
					height: 88rpx;
					background-color: #00B391;
					color: #FFFFFF;
					font-size: 32rpx;
					border-radius: 44rpx;
					display: flex;
					align-items: center;
					justify-content: center;
				}
			}
		}
	}
	
	.progress-bar {
		height: 8rpx;
		background-color: #e8e8e8;
		border-radius: 4rpx;
		margin-bottom: 60rpx;
		position: relative;
		
		.progress-inner {
			position: absolute;
			left: 0;
			top: 0;
			height: 100%;
			background-color: #00B391;
			border-radius: 4rpx;
			transition: width 0.3s ease;
		}
		
		.progress-text {
			position: absolute;
			right: 0;
			top: 20rpx;
			font-size: 24rpx;
			color: #999999;
		}
	}
	
	.question-card {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 40rpx;
		margin-bottom: 60rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
		
		.question-header {
			margin-bottom: 40rpx;
			
			.question-title {
				font-size: 36rpx;
				font-weight: 600;
				color: #333333;
				display: block;
				margin-bottom: 12rpx;
			}
			
			.question-subtitle {
				font-size: 26rpx;
				color: #999999;
				display: block;
				margin-top: 12rpx;
				line-height: 1.4;
			}
			
			.question-note {
				font-size: 24rpx;
				color: #666666;
				display: block;
				margin-top: 20rpx;
				line-height: 1.6;
				background-color: #f8f8f8;
				padding: 20rpx;
				border-radius: 12rpx;
			}
		}
		
		.options-list {
			.option-item {
				display: flex;
				align-items: center;
				padding: 30rpx 0;
				border-bottom: 1rpx solid #f5f5f5;
				
				&:last-child {
					border-bottom: none;
				}
				
				&.selected {
					.option-text {
						color: #00B391;
					}
				}
				
				.option-radio {
					width: 40rpx;
					height: 40rpx;
					border: 2rpx solid #00B391;
					border-radius: 50%;
					margin-right: 20rpx;
					display: flex;
					align-items: center;
					justify-content: center;
					
					.radio-inner {
						width: 24rpx;
						height: 24rpx;
						background-color: #00B391;
						border-radius: 50%;
					}
				}
				
				.option-checkbox {
					width: 40rpx;
					height: 40rpx;
					border: 2rpx solid #00B391;
					border-radius: 8rpx;
					margin-right: 20rpx;
					display: flex;
					align-items: center;
					justify-content: center;
					
					.checkbox-inner {
						width: 24rpx;
						height: 24rpx;
						background-color: #00B391;
						border-radius: 4rpx;
					}
				}
				
				.option-text {
					font-size: 32rpx;
					color: #333333;
					line-height: 1.4;
					font-weight: 500;
				}
			}
		}
	}
	
	.action-buttons {
		display: flex;
		justify-content: space-between;
		gap: 20rpx;
		
		.action-btn {
			flex: 1;
			height: 88rpx;
			font-size: 32rpx;
			border-radius: 44rpx;
			
			&.prev-btn {
				background-color: #FFFFFF;
				color: #00B391;
				border: 2rpx solid #00B391;
			}
			
			&.next-btn {
				background-color: #00B391;
				color: #FFFFFF;
			}
			
			&[disabled] {
				opacity: 0.5;
			}
		}
	}
}
</style>


