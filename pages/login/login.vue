<template>
	<view class="login-page">
		<!-- 顶部渐变背景区域 -->
		<view class="hero">
			<view class="logo-circle">
				<text class="logo-text">My</text>
			</view>
			<text class="hero-title">欢迎回来</text>
			<text class="hero-sub">登录以继续使用</text>
		</view>

		<!-- 登录卡片 -->
		<view class="card">
			<view class="form-item">
				<view class="input-wrap">
					<text class="icon">👤</text>
					<input class="input" v-model="username" type="text" placeholder="请输入用户名"
						placeholder-class="ph" @confirm="handleLogin" />
				</view>
			</view>

			<view class="form-item">
				<view class="input-wrap">
					<text class="icon">🔒</text>
					<input class="input" v-model="password" :password="true" placeholder="请输入密码"
						placeholder-class="ph" @confirm="handleLogin" />
				</view>
			</view>

			<button class="login-btn" :loading="loading" :disabled="loading" @click="handleLogin">
				{{ loading ? '登录中...' : '登 录' }}
			</button>

			<view class="tip-row">
				<text class="tip">默认账号：admin / 123456</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				username: 'admin',
				password: '123456',
				loading: false
			}
		},
		onLoad() {
			// 如果已登录，直接进入首页（tabBar 页面必须用 switchTab）
			if (uni.getStorageSync('token')) {
				uni.switchTab({ url: '/pages/home/home' })
			}
		},
		methods: {
			handleLogin() {
				const username = (this.username || '').trim()
				const password = (this.password || '').trim()

				if (!username) {
					uni.showToast({ title: '请输入用户名', icon: 'none' })
					return
				}
				if (!password) {
					uni.showToast({ title: '请输入密码', icon: 'none' })
					return
				}

				this.loading = true

				// 演示用校验，实际项目请替换为真实接口 uni.request / uni.login
				setTimeout(() => {
					this.loading = false

					if (username === 'admin' && password === '123456') {
						uni.setStorageSync('token', 'mock-token-' + Date.now())
						uni.setStorageSync('username', username)
						uni.showToast({ title: '登录成功', icon: 'success' })
						setTimeout(() => {
							// tabBar 页面只能用 switchTab 跳转，navigateTo 会失败
							uni.switchTab({ url: '/pages/home/home' })
						}, 500)
					} else {
						uni.showToast({ title: '用户名或密码错误', icon: 'none' })
					}
				}, 600)
			}
		}
	}
</script>

<style>
	.login-page {
		min-height: 100vh;
		background-color: #f0f2f5;
		display: flex;
		flex-direction: column;
	}

	/* 顶部渐变区域 */
	.hero {
		background: linear-gradient(135deg, #4e6ef2 0%, #6a5acd 100%);
		/* navigationStyle:custom 后内容顶到屏幕最上方，用状态栏高度变量留出空间 */
		padding: calc(60rpx + var(--status-bar-height)) 60rpx 120rpx;
		display: flex;
		flex-direction: column;
		align-items: center;
		border-bottom-left-radius: 40rpx;
		border-bottom-right-radius: 40rpx;
	}

	.logo-circle {
		width: 120rpx;
		height: 120rpx;
		border-radius: 50%;
		background-color: rgba(255, 255, 255, 0.2);
		border: 4rpx solid rgba(255, 255, 255, 0.5);
		display: flex;
		align-items: center;
		justify-content: center;
		margin-bottom: 30rpx;
	}

	.logo-text {
		font-size: 44rpx;
		font-weight: bold;
		color: #ffffff;
	}

	.hero-title {
		font-size: 44rpx;
		font-weight: bold;
		color: #ffffff;
		margin-bottom: 12rpx;
	}

	.hero-sub {
		font-size: 28rpx;
		color: rgba(255, 255, 255, 0.8);
	}

	/* 登录卡片 */
	.card {
		background-color: #ffffff;
		border-radius: 24rpx;
		padding: 60rpx 40rpx;
		margin: -60rpx 40rpx 0;
		box-shadow: 0 8rpx 30rpx rgba(0, 0, 0, 0.08);
	}

	.form-item {
		margin-bottom: 36rpx;
	}

	.input-wrap {
		display: flex;
		align-items: center;
		background-color: #f5f7fa;
		border-radius: 16rpx;
		padding: 0 24rpx;
		height: 96rpx;
		border: 2rpx solid transparent;
		transition: border-color 0.2s;
	}

	.input-wrap:focus-within {
		border-color: #4e6ef2;
	}

	.icon {
		font-size: 36rpx;
		margin-right: 16rpx;
	}

	.input {
		flex: 1;
		height: 96rpx;
		font-size: 30rpx;
		color: #333333;
	}

	.ph {
		color: #bbbbbb;
		font-size: 28rpx;
	}

	.login-btn {
		margin-top: 20rpx;
		height: 96rpx;
		line-height: 96rpx;
		background: linear-gradient(135deg, #4e6ef2 0%, #6a5acd 100%);
		color: #ffffff;
		font-size: 32rpx;
		border-radius: 16rpx;
		border: none;
	}

	.login-btn[disabled] {
		background: linear-gradient(135deg, #a0b4ef 0%, #b0a0e0 100%);
		color: #ffffff;
	}

	.tip-row {
		margin-top: 30rpx;
		display: flex;
		justify-content: center;
	}

	.tip {
		font-size: 24rpx;
		color: #999999;
	}
</style>
