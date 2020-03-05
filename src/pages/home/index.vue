<template>
	<view>
		<view class="home_tab">
			<view class="home_tab_title">
				<view class="title_inner">
					<uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" styleType="text" activeColor="#e1397d"></uni-segmented-control>
				</view>
				<view class="iconfont iconsearch"></view>
			</view>
			<view class="home_tab_content">
				<view v-if="current === 0">
					<home-recommend></home-recommend>
				</view>
				<view v-if="current === 1">
					<home-category></home-category>
				</view>
				<view v-if="current === 2">
					<home-new></home-new>
				</view>
				<view v-if="current === 3">
					<home-album></home-album>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import {
		uniSegmentedControl
	} from '@dcloudio/uni-ui'
	import homeAlbum from "./home-album"
	import homeCategory from "./home-category"
	import homeNew from "./home-new"
	import homeRecommend from "./home-recommend"
	export default {
		components: {
			uniSegmentedControl,
			homeAlbum,
			homeCategory,
			homeNew,
			homeRecommend
		},
		data() {
			return {
				items: ['推荐', '分类', '最新', '专辑'],
				current: 1
			}
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: this.items[this.current]
			})
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
				uni.setNavigationBarTitle({
					title: this.items[this.current]
				})
			}
		}
	}
</script>

<style lang="scss">
	.home_tab {
		.home_tab_title {
			position: relative;

			.title_inner {
				width: 60%;
				margin: 0 auto;
			}

			.iconsearch {
				position: absolute;
				top: 50%;
				transform: translateY(-50%);
				right: 5%;
			}
		}
	}
</style>
