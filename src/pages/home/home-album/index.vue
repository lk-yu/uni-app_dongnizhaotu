<template>
	<scroll-view class="album_scroll_view" scrollY @scrolltolower="handleToLower">
		<!-- swiper轮播 开始 -->
		<view class="album_swiper">
			<swiper autoplay indicator-dots circular>
				<swiper-item v-for="item in banners" :key="item.id">
					<image :src="item.thumb"></image>
				</swiper-item>
			</swiper>
		</view>
		<!-- swiper轮播 结束 -->

		<!-- 列表 开始 -->
		<view class="album_list">
			<navigator class="album_item" v-for="item in albums" :key="item.id" :url="`/pages/album/index?id=${item.id}`">
				<view class="album_img">
					<image mode="aspectFill" :src="item.cover"></image>
				</view>
				<view class="album_info">
					<view class="album_name">
						{{item.name}}
					</view>
					<view class="album_desc">
						{{item.desc}}
					</view>
					<view class="album_btn">
						<view class="album_attention">
							关注
						</view>
					</view>
				</view>
			</navigator>
		</view>
		<!-- 列表 结束 -->
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				banners: [],
				albums: [],
				params: {
					limit: 30,
					order: 'new',
					skip: 0
				},
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
					url: 'http://service.picasso.adesk.com/v1/wallpaper/album',
					data: this.params
				}).then(
					result => {
						console.log(result)
						if (result.res.album.length === 0) {
							this.hasMore = false
							uni.showToast({
								title: "都被你看完啦!",
								icon: "none"
							})
							return
						}
						if (this.banners.length == 0) {
							this.banners = result.res.banner
						}
						this.albums = [...this.albums, ...result.res.album]

					}
				)
			},
			// 滚动事件
			handleToLower() {
				if (this.hasMore) {
					this.params.skip += this.params.limit
					this.getList()
				} else {
					uni.showToast({
						title: "都被你看完啦!",
						icon: "none"
					})
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	.album_scroll_view {
		height: 100vh;
	}

	.album_swiper {
		swiper {
			height: 326.1rpx;

			image {
				height: 100%;
			}
		}
	}

	.album_list {
		padding: 10rpx;

		.album_item {
			padding: 10rpx 0;
			display: flex;
			border-bottom: 1rpx solid #ccc;

			.album_img {
				flex: 1;
				padding: 10rpx 0;

				image {
					width: 200rpx;
					height: 200rpx;
				}
			}

			.album_info {
				flex: 2;
				padding: 0 10rpx;
				overflow: hidden;

				.album_name {
					font-size: 30rpx;
					color: #000;
					padding: 10rpx 0;
				}

				.album_desc {
					font-size: 24rpx;
					padding: 10rpx 0;
					text-overflow: ellipsis;
					overflow: hidden;
					white-space: nowrap;
				}

				.album_btn {
					padding: 10rpx;
					display: flex;
					justify-content: flex-end;

					.album_attention {
						color: $color;
						border: 1rpx solid $color;
						padding: 10rpx;
					}
				}
			}
		}
	}
</style>
