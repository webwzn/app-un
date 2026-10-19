<template>
	<view class="page">
		<view class="card">
			<!-- 账号：只读展示 -->
			<view class="form-item">
				<text class="label">账号</text>
				<view class="readonly">
					<text class="readonly-text">{{ form.account }}</text>
					<text class="readonly-tip">不可修改</text>
				</view>
			</view>

			<!-- 姓名：文字 -->
			<view class="form-item">
				<text class="label">姓名</text>
				<input class="input" v-model="form.name" type="text" placeholder="请输入姓名"
					placeholder-class="ph" />
			</view>

			<!-- 年龄：数字 -->
			<view class="form-item">
				<text class="label">年龄</text>
				<input class="input" v-model="form.age" type="number" placeholder="请输入年龄"
					placeholder-class="ph" />
			</view>

			<!-- 专业：下拉选择 -->
			<view class="form-item">
				<text class="label">专业</text>

				<!-- H5 / App 端用 picker 原生选择器，各端统一 -->
				<picker mode="selector" :range="majorList" :value="majorIndex" @change="onMajorChange">
					<view class="picker">
						<text :class="form.major ? 'picker-text' : 'picker-tip'">
							{{ form.major || '请选择专业' }}
						</text>
						<text class="arrow">▼</text>
					</view>
				</picker>
			</view>

			<button class="save-btn" @click="handleSave">保 存</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				majorList: ['专业1', '专业2'],   // 下拉选项
				majorIndex: 0,                   // 当前选中项的下标
				form: {
					account: '',   // 只读，登录时带入
					name: '',
					age: '',
					major: ''
				}
			}
		},
		onShow() {
			// 账号取登录时存下的用户名
			this.form.account = uni.getStorageSync('username') || '未登录'

			// 读取之前保存过的资料
			const saved = uni.getStorageSync('userProfile')
			if (saved) {
				this.form = Object.assign({}, this.form, saved, { account: this.form.account })
				// 回填下拉选中状态
				const idx = this.majorList.indexOf(this.form.major)
				if (idx > -1) this.majorIndex = idx
			}
		},
		methods: {
			// 下拉选择变化：e.detail.value 是选中的下标
			onMajorChange(e) {
				this.majorIndex = Number(e.detail.value)
				this.form.major = this.majorList[this.majorIndex]
			},

			handleSave() {
				const name = (this.form.name || '').trim()
				const age = (this.form.age || '').trim()

				// 校验
				if (!name) {
					uni.showToast({ title: '请输入姓名', icon: 'none' })
					return
				}
				if (!age) {
					uni.showToast({ title: '请输入年龄', icon: 'none' })
					return
				}
				if (!/^\d+$/.test(age) || Number(age) <= 0 || Number(age) > 150) {
					uni.showToast({ title: '请输入正确的年龄', icon: 'none' })
					return
				}
				if (!this.form.major) {
					uni.showToast({ title: '请选择专业', icon: 'none' })
					return
				}

				// 组装要保存的数据（年龄转成数字类型）
				const profile = {
					name: name,
					age: Number(age),
					major: this.form.major
				}

				// 演示：存到本地 Storage，真实项目换成 uni.request 提交接口
				uni.setStorageSync('userProfile', profile)

				uni.showToast({ title: '保存成功', icon: 'success' })
				console.log('提交的数据：', profile)
			}
		}
	}
</script>

<style>
	.page {
		min-height: 100vh;
		background-color: #f5f7fa;
		padding: 30rpx 40rpx;
		box-sizing: border-box;
	}

	.card {
		background-color: #ffffff;
		border-radius: 20rpx;
		padding: 50rpx 40rpx 60rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.06);
	}

	.form-item {
		margin-bottom: 40rpx;
	}

	.label {
		display: block;
		font-size: 28rpx;
		color: #666666;
		margin-bottom: 16rpx;
	}

	.input {
		height: 88rpx;
		background-color: #f5f7fa;
		border-radius: 12rpx;
		padding: 0 24rpx;
		font-size: 30rpx;
		color: #333333;
	}

	.ph {
		color: #bbbbbb;
		font-size: 28rpx;
	}

	/* 只读展示的账号样式 */
	.readonly {
		height: 88rpx;
		background-color: #f0f2f5;
		border-radius: 12rpx;
		padding: 0 24rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.readonly-text {
		font-size: 30rpx;
		color: #999999;
	}

	.readonly-tip {
		font-size: 24rpx;
		color: #c0c0c0;
	}

	/* 下拉选择框 */
	.picker {
		height: 88rpx;
		background-color: #f5f7fa;
		border-radius: 12rpx;
		padding: 0 24rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.picker-text {
		font-size: 30rpx;
		color: #333333;
	}

	.picker-tip {
		font-size: 28rpx;
		color: #bbbbbb;
	}

	.arrow {
		font-size: 22rpx;
		color: #999999;
	}

	.save-btn {
		margin-top: 20rpx;
		height: 88rpx;
		line-height: 88rpx;
		background-color: #2979ff;
		color: #ffffff;
		font-size: 32rpx;
		border-radius: 12rpx;
	}
</style>
