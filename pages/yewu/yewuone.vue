<template>
	<view class="page">
		<!-- 列表：用 view 模拟标题，uni-app 不支持 h1 标签 -->
		<view class="item" v-for="(item, index) in list" :key="index" @click="goDetail(item)">
			<text class="item-title">{{ item.title }}</text>
			<text class="item-desc">{{ item.desc }}</text>
			<text class="item-arrow">查看详情 ›</text>
		</view>

		<!-- 底部状态提示 -->
		<view class="footer">
			<text v-if="status === 'loading'" class="footer-text">正在加载中...</text>
			<text v-else-if="status === 'noMore'" class="footer-text">── 没有更多了 ──</text>
			<text v-else class="footer-text">上滑加载更多</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],         // 已加载的数据
				page: 1,          // 当前页码
				pageSize: 8,      // 每页条数
				status: 'more',   // more=可加载 loading=加载中 noMore=没有更多
				isLoading: false, // 防止重复请求
				autoFilling: false // 是否正在自动补足首屏
			}
		},
		onLoad() {
			// 首次加载后，检查是否撑满屏幕，没满就继续加载
			this.loadData().then(() => {
				this.checkFillScreen()
			})
		},
		// ★ 上滑到底部时触发
		onReachBottom() {
			// 自动补足过程中不再响应触底，避免重复请求
			if (this.autoFilling) return
			this.loadData()
		},
		// ★ 下拉刷新时触发（需在 pages.json 开启 enablePullDownRefresh）
		onPullDownRefresh() {
			this.refresh()
		},
		methods: {
			// 下拉刷新：重置状态后重新加载第 1 页
			refresh() {
				if (this.isLoading) {
					uni.stopPullDownRefresh()
					return
				}

				this.page = 1
				this.list = []
				this.status = 'more'

				this.loadData(true).then(() => {
					this.checkFillScreen()
				})
			},

			// 加载数据，返回 Promise，便于链式调用
			loadData(isRefresh = false) {
				return new Promise((resolve) => {
					// 正在加载 或 已无更多 → 直接结束
					if (this.isLoading || this.status === 'noMore') {
						if (isRefresh) uni.stopPullDownRefresh()
						resolve()
						return
					}

					this.isLoading = true
					this.status = 'loading'

					// 模拟网络延迟 800ms
					setTimeout(() => {
						const newList = this.mockRequest(this.page, this.pageSize)

						if (newList.length === 0) {
							this.status = 'noMore'
						} else {
							this.list = this.list.concat(newList)
							this.page++
							this.status = 'more'
						}

						this.isLoading = false

						// ★ 下拉刷新完成后必须手动停止动画，否则会一直转
						if (isRefresh) {
							uni.stopPullDownRefresh()
							uni.showToast({ title: '刷新成功', icon: 'success' })
						}

						resolve()
					}, 800)
				})
			},

			// ★ 检查内容是否撑满屏幕，没满就自动加载下一页
			checkFillScreen() {
				if (this.status === 'noMore' || this.isLoading) return

				this.autoFilling = true

				const query = uni.createSelectorQuery().in(this)
				query.select('.page').boundingClientRect()
				query.selectViewport().scrollOffset()
				query.exec((res) => {
					if (!res || !res[0]) {
						this.autoFilling = false
						return
					}

					const contentHeight = res[0].height
					const screenHeight = uni.getSystemInfoSync().windowHeight

					// 内容高度还没超过屏幕 → 继续加载下一页
					if (contentHeight <= screenHeight && this.status !== 'noMore') {
						this.loadData().then(() => {
							this.checkFillScreen()  // 递归检查，直到撑满或没有更多
						})
					} else {
						this.autoFilling = false
					}
				})
			},

			// ★ 点击列表项，用 EventChannel 传递参数（支持传对象 + 反向回传）
			goDetail(item) {
				uni.navigateTo({
					// 方式三：EventChannel，URL 不再拼接任何参数
					url: '/pages/yewu/detail',
					// events：监听详情页发回来的事件
					events: {
						// 详情页点「刷新列表」时会触发这里
						refreshList: () => {
							this.refresh()
						}
					},
					success: (res) => {
						// ★ 页面打开后，通过 eventChannel 把整条数据发过去
						res.eventChannel.emit('sendData', { data: item })
					}
				})
			},

			// 模拟后端返回，最多 4 页共 30 条
			mockRequest(page, pageSize) {
				const total = 30
				const start = (page - 1) * pageSize
				if (start >= total) return []

				const count = Math.min(pageSize, total - start)
				const result = []
				for (let i = 0; i < count; i++) {
					const no = start + i + 1
					result.push({
						id: no,                                  // ★ 唯一标识，用于传参
						title: '第 ' + no + ' 条数据',
						desc: '这是第 ' + page + ' 页加载进来的内容'
					})
				}
				return result
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

	.item {
		background-color: #ffffff;
		border-radius: 16rpx;
		padding: 30rpx;
		margin-bottom: 20rpx;
		display: flex;
		flex-direction: column;
	}

	.item-title {
		font-size: 36rpx;
		font-weight: bold;
		color: #333333;
		margin-bottom: 10rpx;
	}

	.item-desc {
		font-size: 26rpx;
		color: #999999;
	}

	.item-arrow {
		font-size: 24rpx;
		color: #2979ff;
		margin-top: 16rpx;
	}

	.footer {
		padding: 30rpx 0 60rpx;
		display: flex;
		justify-content: center;
	}

	.footer-text {
		font-size: 26rpx;
		color: #999999;
	}
</style>
