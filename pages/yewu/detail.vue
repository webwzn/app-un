<template>
	<view class="page">
		<view class="card">
			<!-- 参数展示区：证明数据确实通过 EventChannel 传过来了 -->
			<view class="param-box">
				<text class="param-label">接收方式：EventChannel</text>
				<text class="param-line">id：{{ detail.id }}</text>
				<text class="param-line">title：{{ detail.title }}</text>
				<text class="param-line">desc：{{ detail.desc }}</text>
			</view>

			<text class="title">{{ detail.title }}</text>

			<view class="divider"></view>

			<text class="section-title">详细内容</text>
			<text class="content">{{ detail.content }}</text>

			<view class="info-row">
				<text class="info-label">数据编号</text>
				<text class="info-value">#{{ detail.id }}</text>
			</view>
			<view class="info-row">
				<text class="info-label">来源页面</text>
				<text class="info-value">业务1 列表页</text>
			</view>
		</view>

		<button class="refresh-btn" @click="emitRefresh">刷新列表并返回</button>
		<button class="back-btn" @click="goBack">仅返回</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				detail: {
					id: '',
					title: '',
					desc: '',
					content: ''
				},
				channel: null   // 保存 eventChannel 引用，便于反向发送
			}
		},
		onLoad() {
			// ★ 拿到与上一个页面之间的通信通道
			this.channel = this.getOpenerEventChannel()

			// ★ 监听列表页 emit 的 'sendData' 事件，接收数据
			this.channel.on('sendData', (res) => {
				console.log('EventChannel 收到的数据：', res)
				const item = res.data || {}

				this.detail = {
					id: item.id || '',
					title: item.title || '未收到标题',
					desc: item.desc || '未收到描述',
					content: '这是第 ' + (item.id || '?') + ' 条数据的详细内容。' +
						'这段数据是通过 EventChannel 从列表页传过来的，' +
						'没有经过 URL 拼接，所以可以传任意复杂的对象。'
				}

				// 动态修改导航栏标题
				if (item.id) {
					uni.setNavigationBarTitle({ title: '详情 #' + item.id })
				}
			})
		},
		methods: {
			// ★ 反向通知：告诉列表页「该刷新了」，然后返回
			emitRefresh() {
				if (this.channel) {
					this.channel.emit('refreshList')   // 触发列表页的 events.refreshList
				}
				uni.navigateBack()
			},

			// 只返回，不通知刷新
			goBack() {
				uni.navigateBack()
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
		padding: 40rpx;
		box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.06);
	}

	.param-box {
		background-color: #f0f7ff;
		border-radius: 12rpx;
		padding: 24rpx;
		margin-bottom: 40rpx;
		display: flex;
		flex-direction: column;
	}

	.param-label {
		font-size: 24rpx;
		color: #2979ff;
		margin-bottom: 12rpx;
	}

	.param-line {
		font-size: 26rpx;
		color: #555555;
		line-height: 40rpx;
	}

	.title {
		display: block;
		font-size: 40rpx;
		font-weight: bold;
		color: #333333;
	}

	.divider {
		height: 1rpx;
		background-color: #eeeeee;
		margin: 40rpx 0;
	}

	.section-title {
		display: block;
		font-size: 30rpx;
		font-weight: bold;
		color: #333333;
		margin-bottom: 20rpx;
	}

	.content {
		font-size: 28rpx;
		color: #666666;
		line-height: 48rpx;
	}

	.info-row {
		display: flex;
		justify-content: space-between;
		padding: 24rpx 0;
		border-top: 1rpx solid #f2f2f2;
		margin-top: 20rpx;
	}

	.info-label {
		font-size: 28rpx;
		color: #999999;
	}

	.info-value {
		font-size: 28rpx;
		color: #333333;
	}

	.refresh-btn {
		margin-top: 40rpx;
		height: 88rpx;
		line-height: 88rpx;
		background-color: #2979ff;
		color: #ffffff;
		font-size: 30rpx;
		border-radius: 12rpx;
	}

	.back-btn {
		margin-top: 20rpx;
		height: 88rpx;
		line-height: 88rpx;
		background-color: #ffffff;
		color: #666666;
		font-size: 30rpx;
		border-radius: 12rpx;
		border: 1rpx solid #e5e5e5;
	}
</style>
