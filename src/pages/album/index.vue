<template>
	<view class="album_view" v-if="wallpapers.length > 0">
		<!-- 专辑背景 开始 -->
		<view class="album_bg">
			<image mode="widthFix" :src="albums.cover"></image>
			<view class="album_info">
				<view class="album_name">
					{{albums.name}}
				</view>
				<view class="album_btn">
					关注专辑
				</view>
			</view>
		</view>
		<!-- 专辑背景 结束 -->

		<!-- 专辑作者 开始 -->
		<view class="album_author">
			<view class="album_author_info">
				<image mode="widthFix" :src="albums.user.avatar"></image>
				<view class="author_name">
					{{albums.user.name}}
				</view>
			</view>
			<view class="album_author_desc">
				<text>{{albums.desc}}</text>
			</view>
		</view>
		<!-- 专辑作者 结束 -->

		<!-- 列表 开始 -->
		<view class="album_list">
			<view class="album_item" v-for="(item,index) in wallpapers" :key="item.id">
				<go-detail :list="wallpapers" :index="index">
					<image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)"></image>
				</go-detail>
			</view>
		</view>
		<!-- 列表 结束 -->
	</view>
</template>

<script>
	import goDetail from '@/components/goDetail'
	export default {
		components: {
			goDetail
		},
		data() {
			return {
				params: {
					limit: 30,
					order: 'new',
					skip: 0,
					first: 1
				},
				id: -1,
				albums: {},
				wallpapers: [],
				hasMore: true
			}
		},
		onLoad(params) {
			console.log(params.id)
			this.id = params.id
			this.getList()
		},
		onReachBottom() {
			if (this.hasMore) {
				this.params.first = 0
				this.params.skip += this.params.limit
				this.getList()
			} else {
				uni.showToast({
					title: "都被你看完啦!",
					icon: "none"
				})
			}
		},
		methods: {
			getList() {
				this.request({
					url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
					data: this.params
				}).then(result => {
					console.log(result)
					if (Object.keys(this.albums).length === 0) {
						this.albums = result.res.album
					}
					if (result.res.wallpaper.length === 0) {
						this.hasMore = false
						uni.showToast({
							title: "都被你看完啦!",
							icon: "none"
						})
						return
					}
					this.wallpapers = [...this.wallpapers, ...result.res.wallpaper]
				})
			}
		},
	}
</script>

<style lang="scss" scoped>
	page,
	.album_view {
		height: 100%;
	}

	// 专辑背景 开始
	.album_bg {
		position: relative;

		image {}
	}

	.album_info {
		position: absolute;
		width: 100%;
		left: 0;
		bottom: 0;
		display: flex;
		justify-content: space-between;
		height: 80rpx;
		align-items: center;
		color: #fff;
		padding: 0 15rpx;

		.album_name {
			font-size: 40rpx;
		}

		.album_btn {
			background-color: $color;
			width: 152rpx;
			height: 50rpx;
			display: flex;
			justify-content: center;
			align-items: center;
			border-radius: 10rpx;
		}
	}

	// 专辑背景 结束

	// 专辑作者 开始
	.album_author {
		padding: 10rpx;

		.album_author_info {
			padding: 10rpx 0;
			display: flex;

			image {
				width: 50rpx;
			}

			.album_name {
				color: #000;
				margin-left: 15rpx;
			}
		}

		.album_author_desc {}
	}

	// 专辑作者 结束

	// 列表 开始
	.album_list {
		display: flex;
		flex-wrap: wrap;

		.album_item {
			width: 33.33%;
			border: 3rpx solid #fff;

			image {
				height: 160rpx;
			}
		}
	}

	// 列表 结束
</style>
