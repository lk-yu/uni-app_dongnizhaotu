<template>
	<view>
		<!-- 用户信息 开始 -->
		<view class="user_info">
			<view class="user_icon">
				<image :src="imgDetails.user.avatar" mode="widthFix"></image>
			</view>
			<view class="user_desc">
				<view class="user_name">
					{{imgDetails.user.name}}
				</view>
				<view class="user_atime">
					{{imgDetails.cnTime}}
				</view>
			</view>
		</view>
		<!-- 用户信息 结束 -->

		<!-- 高清大图  开始-->
		<view class="high_img">
			<swiper-action @swiperAction="handleSwiperAction">
				<image mode="widthFix" :src="imgDetails.thumb"></image>
			</swiper-action>
		</view>
		<!-- 高清大图  结束-->

		<!-- 点赞 开始 -->
		<view class="user_rank">
			<view class="rank">
				<text class="iconfont icondianzan">{{imgDetails.rank}}</text>
			</view>
			<view class="user_collect">
				<text class="iconfont iconshoucang">收藏</text>
			</view>
		</view>
		<!-- 点赞 结束 -->

		<!-- 专辑 开始 -->
		<view class="album_wrap" v-if="albums.length">
			<!-- 标题 -->
			<view class="album_title">
				相关
			</view>
			<!-- 内容 -->
			<view class="album_list">
				<view class="album_item" v-for="item in albums" :key="item.id">
					<view class="album_cover">
						<image mode="aspectFill" :src="item.cover"></image>
					</view>
					<!-- 右边 -->
					<view class="album_info">
						<view class="album_info_text">
							专辑
						</view>
						<view class="album_name">
							{{item.name}}
						</view>
						<text class="iconfont iconiconfontjiantou4"></text>
					</view>
				</view>
			</view>
		</view>
		<!-- 专辑 结束 -->

		<!-- 最热评论 开始 -->
		<view class="comment_wrap hot" v-if="hots.length">
			<view class="comment_title">
				<text class="iconfont iconhot1">

				</text>
				<text class="comment_text">最热评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item" v-for="item in hots" :key="item.id">
					<!-- 用户信息 -->
					<view class="comment_user">
						<!-- 用户头像 -->
						<view class="user_icon">
							<image mode="widthFix" :src="item.user.avatar"></image>
						</view>
						<!-- 用户名称 -->
						<view class="user_name">
							<view class="user_nickname">
								{{item.user.name}}
							</view>
							<view class="user_time">
								{{item.cnTime}}
							</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user_badge">
							<image v-for="item2 in item.user.title " :key="item2.icon" :src="item2.icon"></image>
						</view>
					</view>
					<!-- 评论数据 -->
					<view class="comment_desc">
						<view class="comment_content">
							{{item.content}}
						</view>
						<view class="comment_like">
							<text class="iconfont icondianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最热评论 结束 -->

		<!-- 最新评论 开始 -->
		<view class="comment_wrap new" v-if="comments.length">
			<view class="comment_title">
				<text class="iconfont iconpinglun">

				</text>
				<text class="comment_text">最新评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item" v-for="item in comments" :key="item.id">
					<!-- 用户信息 -->
					<view class="comment_user">
						<!-- 用户头像 -->
						<view class="user_icon">
							<image mode="widthFix" :src="item.user.avatar"></image>
						</view>
						<!-- 用户名称 -->
						<view class="user_name">
							<view class="user_nickname">
								{{item.user.name}}
							</view>
							<view class="user_time">
								{{item.cnTime}}
							</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user_badge">
							<image v-for="item2 in item.user.title " :key="item2.icon" :src="item2.icon"></image>
						</view>
					</view>
					<!-- 评论数据 -->
					<view class="comment_desc">
						<view class="comment_content">
							{{item.content}}
						</view>
						<view class="comment_like">
							<text class="iconfont icondianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最新评论 结束 -->

		<!-- 下载 开始 -->
		<view class="download">
			<view class="download_btn" @click="handleDownload">
				下载图片
			</view>
		</view>
		<!-- 下载 结束 -->
	</view>
</template>

<script>
	import moment from "moment"
	import swiperAction from "@/components/swiperAction"
	// 设置 moment库 语言格式
	moment.locale('zh-cn')
	export default {
		components: {
			swiperAction
		},
		data() {
			return {
				// 图片信息对象
				imgDetails: {},
				albums: [],
				comments: [],
				hots: [],
				// 图片的索引
				imgindex: 0
			}
		},
		onLoad() {
			console.log(getApp().globalData)
			const {
				imgIndex
			} = getApp().globalData;
			this.imgIndex = imgIndex
			this.getData()

		},
		methods: {
			getData() {
				const {
					imgList
				} = getApp().globalData;
				this.imgDetails = imgList[this.imgIndex]
				// 转化时间格式
				this.imgDetails.cnTime = moment(this.imgDetails.atime * 1000).fromNow()
				// 获取图片id,获取请求数据
				this.getComments(this.imgDetails.id)
			},
			getComments(id) {
				this.request({
					url: `http://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment`
				}).then(result => {
					console.log(result)
					this.albums = result.res.album
					result.res.comment.forEach(
						item => item.cnTime = moment(item.atime * 1000).fromNow()
					)
					this.comments = result.res.comment
					result.res.hot.forEach(
						item => item.cnTime = moment(item.atime * 1000).fromNow()
					)
					this.hots = result.res.hot
				})
			},
			// 滑动事件
			handleSwiperAction(e) {
				console.log(e)
				if (e.direction === 'left' && this.imgIndex < getApp().globalData.imgList.length - 1) {
					this.imgIndex++
					this.getData()
				} else if (e.direction === 'right' && this.imgIndex > 0) {
					this.imgIndex--
					this.getData()
				} else {
					uni.showToast({
						title: '都被你看完啦',
						icon: 'none'
					})
				}
			},
			// 下载图片事件
			async handleDownload() {
				await uni.showLoading({
					title:"下载中ing..."
				})
				
				const result1 = await uni.downloadFile({
					url: this.imgDetails.img
				})
				const {tempFilePath} = result1[1]

				const result2 = await uni.saveImageToPhotosAlbum({
					filePath: tempFilePath
				})
				uni.hideLoading()
				uni.showToast({
					title:'下载成功'
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	// 用户信息 开始
	.user_info {
		display: flex;
		padding: 20rpx;

		.user_icon {
			padding: 0 20rpx;

			image {
				width: 88rpx;
				border-radius: 50%;
			}
		}

		.user_desc {
			.user_name {
				color: #000;
				font-weight: 600;
			}

			.user_atime {
				color: #ccc;
				font-size: 24rpx;
				padding: 10rpx 0;
			}
		}
	}

	// 用户信息 结束

	// 高清大图 开始
	.high_img {
		image {
			border-bottom: 5rpx solid #ccc;
		}
	}

	// 高清大图 结束

	// 点赞 开始
	.user_rank {
		display: flex;
		height: 80rpx;
		border-bottom: 5rpx solid #eee;

		.rank {
			display: flex;
			justify-content: center;
			align-items: center;
			flex: 1;

			.iconfont {}
		}

		.user_collect {
			display: flex;
			justify-content: center;
			align-items: center;
			flex: 1;

			.iconfont {}
		}
	}

	// 点赞 结束

	// 专辑 开始
	.album_wrap {
		padding: 20rpx;

		.album_title {
			padding: 10rpx 0;
		}

		.album_list {
			.album_item {
				display: flex;
				padding: 10rpx 0;
				border-bottom: 10rpx solid #eee;

				.album_cover {
					flex: 1;

					image {
						width: 180rpx;
						height: 180rpx;
					}
				}

				.album_info {
					flex: 3;
					padding-left: 20rpx;
					position: relative;

					.album_info_text {
						width: 100rpx;
						height: 50rpx;
						background-color: $color;
						color: #fff;
						display: flex;
						justify-content: center;
						align-items: center;
					}

					.album_name {
						padding: 10rpx;
						color: #888;
					}

					.iconfont {
						font-size: 40rpx;
						position: absolute;
						top: 50%;
						transform: translateY(-50%);
						right: 10%;
						color: #000;
					}
				}
			}
		}
	}

	// 专辑 结束

	// 评论 开始
	.comment_wrap {
		.comment_title {
			padding: 15rpx;

			.iconfont {
				color: red;
				font-size: 40rpx;
			}

			.comment_text {
				font-weight: 600;
				font-size: 28rpx;
				color: #666;
				margin-left: 10rpx;
			}
		}

		.comment_list {
			.comment_item {
				border-bottom: 15rpx solid #eee;

				.comment_user {
					display: flex;
					// justify-content: space-around;
					padding: 20rpx 0;

					.user_icon {
						width: 15%;
						display: flex;
						justify-content: center;
						align-items: center;

						image {
							width: 90%;
						}
					}

					.user_name {
						flex: 1;

						.user_nickname {
							color: #777;
						}

						.user_time {
							color: #ccc;
							font-size: 24rpx;
							padding: 5rpx;
						}
					}

					.user_badge {
						display: flex;

						image {
							width: 40rpx;
							height: 40rpx;
						}
					}


				}

				.comment_desc {
					display: flex;
					padding: 30rpx 0;

					.comment_content {
						flex: 1;
						padding-left: 15%;
						color: #000;
					}

					.comment_like {
						text-align: right;

						.iconfont {}
					}
				}
			}
		}
	}

	// 评论 结束

	// 最新评论 开始
	.new {
		.iconpinglun {
			color: aqua !important;
		}
	}

	// 最新评论 结束

	// 下载 开始
	.download {
		height: 120rpx;
		display: flex;
		align-items: center;
		justify-content: center;

		.download_btn {
			width: 90%;
			height: 80%;
			background-color: $color;
			color: #fff;
			font-size: 50rpx;
			font-weight: 600;
			display: flex;
			justify-content: center;
			align-items: center;
		}
	}

	// 下载 结束
</style>
