<template>
	<view class="page">
		<view class="card">
			<text class="title">欢迎回来</text>
			<text class="subtitle">{{ username || '游客' }}</text>
			<text class="desc">这里是首页，下方有两个导航可以切换1</text>




			<!-- 属性：plain镂空、disabled禁用、loading加载中 -->

		</view>
		<button type="primary" class="mt" @click="goyewuone">业务1</button>
		<button type="primary" class="mt" @click="goyewutwo">业务2</button>
		<button type="default" class="mt">业务3</button>
		<button type="warn" class="mt">业务4</button>


		<button class="logout-btn" @click="handleLogout">退出登录</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				username: ''
			}
		},
		onShow() {
			this.username = uni.getStorageSync('username') || ''
		},
		methods: {
			handleLogout() {
				uni.showModal({
					title: '提示',
					content: '确定要退出登录吗？',
					success: (res) => {
						if (res.confirm) {
							uni.removeStorageSync('token')
							uni.removeStorageSync('username')
							// 登录页不是 tabBar 页面，用 reLaunch 清空页面栈
							uni.reLaunch({ url: '/pages/login/login' })
						}
					}
				})
			},
			goyewuone() {
				uni.navigateTo({
					url: '/pages/yewu/yewuone'
				})
			},
			goyewutwo() {
				uni.navigateTo({
					url: '/pages/yewu/yewutwo'
				})
			}
		}
	}
</script>

<style>
	.page {
		min-height: 100vh;
		background-color: #f5f7fa;
		padding: 60rpx 40rpx;
		box-sizing: border-box;
	}

	.card {
		background-color: #ffffff;
		border-radius: 20rpx;
		padding: 60rpx 40rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.06);
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.title {
		font-size: 40rpx;
		font-weight: bold;
		color: #333333;
		margin-bottom: 20rpx;
	}

	.subtitle {
		font-size: 32rpx;
		color: #2979ff;
		margin-bottom: 30rpx;
	}

	.desc {
		font-size: 26rpx;
		color: #999999;
	}

	.logout-btn {
		margin-top: 60rpx;
		height: 88rpx;
		line-height: 88rpx;
		background-color: #ffffff;
		color: #dd524d;
		font-size: 30rpx;
		border-radius: 12rpx;
		border: 1rpx solid #eeeeee;
	}
</style>
