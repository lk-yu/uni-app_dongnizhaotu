<template>
	<view>
		<view class="category_tab">
			<view class="category_tab_title">
				<view class="title_inner">
					<uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" styleType="text" activeColor="#e1397d"></uni-segmented-control>
				</view>
			</view>
			<!-- 内容 开始 -->
			<scroll-view enable-flex scrollY class="category_tab_content" @scrolltolower="handleScrollToLower">
				<view class="category_item" v-for="(item,index) in verticals" :key="item.id">
					<go-detail :list="verticals" :index="index">
						<image mode="widthFix" :src="item.thumb"></image>
					</go-detail>
				</view>
			</scroll-view>
			<!-- 内容 结束 -->
		</view>
	</view>

</template>

<script>
	import {
		uniSegmentedControl
	} from '@dcloudio/uni-ui'
	import goDetail from '@/components/goDetail'
	
	export default {
		components: {
			uniSegmentedControl,
			goDetail
		},
		data() {
			return {
				items: ['最新', '热门'],
				arr: ['new', 'hot'],
				current: 0,
				params: {
					limit: 30,
					order: 'new',
					skip: 0
				},
				id: '',
				verticals: [],
				hasMore: true
			}
		},
		onLoad(params) {
			uni.setNavigationBarTitle({
				title: this.items[this.current]
			})
			console.log(params)
			this.id = params.id
			this.getList(params.id)
		},
		methods: {
			// 分段器事件
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;

					uni.setNavigationBarTitle({
						title: this.items[this.current]
					})
					this.params.order = this.arr[this.current]
					this.verticals = []
					this.params.skip = 0
					this.getList(this.id)
				}

			},
			// 获取数据事件
			getList(id) {
				this.request({
					url: `http://157.122.54.189:9088/image/v1/vertical/category/${id}/vertical`,
					data: this.params
				}).then(result => {
					console.log(result)
					if (result.res.vertical.length === 0) {
						this.hasMore = false
						uni.showToast({
							title: '都被你看完啦!',
							icon: 'none'
						})
						return
					}
					this.verticals = [...this.verticals, ...result.res.vertical]
				})
			},
			// 滑动加载事件
			handleScrollToLower() {
				if (this.hasMore) {
					this.params.skip += this.params.limit
					this.getList(this.id)
				} else {
					uni.showToast({
						title: '都被你看完啦!',
						icon: 'none'
					})
				}
			}
		}
	}
</script>

<style lang="scss">
	.category_tab {
		.category_tab_title {
			position: relative;

			.title_inner {
				width: 60%;
				margin: 0 auto;
			}
		}

		.category_tab_content {
			display: flex;
			flex-wrap: wrap;
			height: calc(100vh - 36px);

			.category_item {
				width: 33.33%;
				border: 5rpx solid #fff;

				image {}
			}
		}
	}
</style>
