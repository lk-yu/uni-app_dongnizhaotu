<template>
	<scroll-view scrollY @scrolltolower="handleToLower" class="recommend_view" v-if="recommends.length >0 ">
		<!-- 推荐 开始 -->
		<view class="recommend_wrap">
			<navigator class="recommend_item" v-for="item in recommends" :key="item.id" :url="`/pages/album/index?id=${item.target}`">
				<image mode="widthFix" :src="item.thumb"></image>
			</navigator>
		</view>
		<!-- 推荐 结束 -->

		<!-- 月份 开始 -->
		<view class="monthes_wrap">
			<view class="monthes_title">
				<view class="monthes_title_info">
					<view class="monthes_info">
						<text> {{monthes.DD}} / </text>
						{{monthes.MM}} 月
					</view>
					<view class="monthes_text">
						{{monthes.title}}
					</view>
				</view>
				<view class="monthes_title_more">
					更多 >
				</view>
			</view>
			<view class="monthes_content">
				<view class="monthes-item" v-for="(item,index) in monthes.items" :key="item.id">
					<go-detail :list="monthes.items" :index="index">
						<image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)"></image>
					</go-detail>
				</view>
			</view>
		</view>
		<!-- 月份 结束 -->

		<!-- 热门 开始 -->
		<view class="hots_wrap">
			<view class="hots_title">
				<text>热门</text>
			</view>
			<view class="hots_content">
				<view class="hots_item" v-for="(item,index) in hots" :key="item.id">
					<go-detail :list="hots" :index="index">
						<image mode="widthFix" :src="item.thumb"></image>
					</go-detail>
				</view>
			</view>
		</view>
		<!-- 热门 结束 -->
	</scroll-view>
</template>

<script>
	import moment from 'moment';
	import goDetail from '@/components/goDetail'
	export default {
		components: {
			goDetail
		},
		data() {
			return {
				// 推荐数据
				recommends: [],
				// 月份数据
				monthes: {},
				// 热门数据
				hots: [],
				// 请求参数
				params: {
					// 要获取几条数据
					limit: 30,
					order: 'hot',
					skip: 0
				},
				// 是否还有下一页
				hasMore: true
			}
		},
		mounted() {
			this.getList()
		},
		methods: {
			// 获取接口的数据
			getList() {
				this.request({
					url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
					data: this.params
				}).then(result => {
					console.log(result)
					// 还有没有下一页数据
					if (result.res.vertical.length === 0) {
						this.hasMore = false
						uni.showToast({
							title: "都被你看完啦!",
							icon: "none"
						})
						return
					}
					if (this.recommends.length === 0) {
						// 推荐模块
						this.recommends = result.res.homepage[1].items
						// 月份模块
						this.monthes = result.res.homepage[2]
						// 将时间戳 改成 18号/月 monent.js
						this.monthes.MM = moment(this.monthes.stime).format('MM')
						this.monthes.DD = moment(this.monthes.stime).format('DD')
					}
					// 热门模块
					this.hots = [...this.hots, ...result.res.vertical]
				})

			},
			// 滚动条触底事件
			handleToLower() {
				if (this.hasMore) {
					this.params.skip += this.params.limit
					this.getList()
				} else {
					uni.showToast({
						title: '都被你看光啦!',
						icon: 'none'
					})
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	.recommend_view {
		height: calc(100vh - 36px);
	}

	// 推荐 开始
	.recommend_wrap {
		display: flex;
		flex-wrap: wrap;

		.recommend_item {
			width: 50%;
			border: 5rpx solid #fff;
		}
	}

	// 推荐 结束

	// 月份 开始
	.monthes_wrap {
		.monthes_title {
			display: flex;
			justify-content: space-between;
			padding: 20rpx;

			.monthes_title_info {
				color: $color;
				font-size: 30rpx;
				font-weight: 600;
				display: flex;

				.monthes_info {
					text {
						font-size: 36rpx;
					}
				}

				.monthes_text {
					font-size: 34rpx;
					color: #666;
					margin-left: 30rpx;
				}
			}

			.monthes_title_more {
				font-size: 24rpx;
				color: $color;
			}
		}

		.monthes_content {
			display: flex;
			flex-wrap: wrap;

			.monthes-item {
				width: 33.33%;
				border: 5rpx solid #fff;
			}
		}
	}

	// 月份 结束

	// 热门 开始
	.hots_wrap {
		.hots_title {
			padding: 20rpx;

			text {
				border-left: 20rpx solid $color;
				padding-left: 20rpx;
				font-size: 34rpx;
				font-weight: 600;
			}
		}

		.hots_content {
			display: flex;
			flex-wrap: wrap;

			.hots_item {
				width: 33.3%;
				border: 5rpx solid #fff;

				image {}
			}
		}
	}

	// 热门 结束
</style>
