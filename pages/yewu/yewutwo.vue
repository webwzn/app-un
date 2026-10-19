<template>
	<view class="page">
		<!-- 顶部 tab 导航 -->
		<view class="tab-bar">
			<view class="tab-item" v-for="(tab, index) in tabs" :key="index"
				:class="{ active: current === index }" @click="switchTab(index)">
				<text class="tab-text">{{ tab.name }}</text>
				<view class="tab-line" v-if="current === index"></view>
			</view>
		</view>

		<!-- ★ 内容区：左右滑动切换（swiper） -->
		<swiper class="swiper" :current="current" @change="onSwiperChange">
			<swiper-item v-for="(tab, index) in tabs" :key="index">
				<scroll-view scroll-y class="scroll">
					<view class="panel">
						<view class="panel-head">
							<text class="panel-title">{{ tab.name }}</text>
							<text class="panel-sub">{{ tab.subtitle }}</text>
						</view>

						<!-- 内容列表 -->
						<view class="row" v-for="(item, i) in tab.list" :key="i">
							<text class="row-index">{{ i + 1 }}</text>
							<view class="row-body">
								<text class="row-title">{{ item.title }}</text>
								<text class="row-desc">{{ item.desc }}</text>
							</view>
						</view>
					</view>
				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				current: 0,   // 当前选中的 tab 下标
				tabs: [
					{
						name: '推荐',
						subtitle: '左右滑动可切换',
						list: [
							{ title: '推荐内容一', desc: '这是推荐页的第一条内容' },
							{ title: '推荐内容二', desc: '这是推荐页的第二条内容' },
							{ title: '推荐内容三', desc: '这是推荐页的第三条内容' },
							{ title: '推荐内容四', desc: '这是推荐页的第四条内容' }
						]
					},
					{
						name: '关注',
						subtitle: '已关注的动态',
						list: [
							{ title: '关注动态一', desc: '你关注的人发布了新内容' },
							{ title: '关注动态二', desc: '你关注的人点赞了文章' },
							{ title: '关注动态三', desc: '你关注的人关注了话题' }
						]
					},
					{
						name: '消息',
						subtitle: '系统与私信',
						list: [
							{ title: '系统通知', desc: '你的资料已保存成功' },
							{ title: '私信消息', desc: '有一条新的未读消息' },
							{ title: '互动提醒', desc: '有人评论了你的内容' },
							{ title: '活动推送', desc: '本周活动已经开始' },
							{ title: '版本更新', desc: '新版本已发布，请及时更新' }
						]
					}
				]
			}
		},
		onLoad() {

		},
		methods: {
			// 点击顶部 tab：只是改 current，swiper 会自动滑过去
			switchTab(index) {
				if (this.current === index) return
				this.current = index
			},

			// 手指滑动 swiper 后，同步顶部 tab 高亮
			onSwiperChange(e) {
				this.current = e.detail.current
			}
		}
	}
</script>

<style>
	.page {
		background-color: #f5f7fa;
		height: 100vh;
		display: flex;
		flex-direction: column;
	}

	/* 顶部 tab */
	.tab-bar {
		height: 88rpx;
		background-color: #ffffff;
		display: flex;
		align-items: center;
		border-bottom: 1rpx solid #eeeeee;
	}

	.tab-item {
		flex: 1;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		position: relative;
	}

	.tab-text {
		font-size: 30rpx;
		color: #666666;
	}

	.tab-item.active .tab-text {
		color: #2979ff;
		font-weight: bold;
	}

	.tab-line {
		position: absolute;
		bottom: 0;
		width: 60rpx;
		height: 6rpx;
		background-color: #2979ff;
		border-radius: 3rpx;
	}

	/* swiper 占满剩余空间 */
	.swiper {
		flex: 1;
	}

	.scroll {
		height: 100%;
	}

	.panel {
		padding: 30rpx 40rpx 60rpx;
	}

	.panel-head {
		margin-bottom: 30rpx;
	}

	.panel-title {
		display: block;
		font-size: 40rpx;
		font-weight: bold;
		color: #333333;
		margin-bottom: 10rpx;
	}

	.panel-sub {
		font-size: 26rpx;
		color: #999999;
	}

	.row {
		background-color: #ffffff;
		border-radius: 16rpx;
		padding: 30rpx;
		margin-bottom: 20rpx;
		display: flex;
		align-items: center;
	}

	.row-index {
		width: 56rpx;
		height: 56rpx;
		line-height: 56rpx;
		text-align: center;
		background-color: #eaf2ff;
		color: #2979ff;
		font-size: 26rpx;
		border-radius: 50%;
		margin-right: 24rpx;
	}

	.row-body {
		display: flex;
		flex-direction: column;
		flex: 1;
	}

	.row-title {
		font-size: 32rpx;
		color: #333333;
		margin-bottom: 8rpx;
	}

	.row-desc {
		font-size: 26rpx;
		color: #999999;
	}
</style>
