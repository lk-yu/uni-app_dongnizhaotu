<template>
	<view>
		<!-- 分类开始 -->
		<view class="category_wrap">
			<navigator class="category_item" v-for="item in categorys" :key="item.id" :url="`/pages/imgCategory/index?id=${item.id}`">
				<image mode="aspectFill" :src="item.cover"></image>
				<view class="category_name">
					{{item.name}}
				</view>
			</navigator>
		</view>
		<!-- 分类结束 -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				categorys: []
			}
		},
		mounted() {
			this.getList()
		},
		methods: {
			getList() {
				this.request({
					url: 'http://157.122.54.189:9088/image/v1/vertical/category'
				}).then(result => {
					console.log(result)
					this.categorys = result.res.category
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.category_wrap {
		display: flex;
		flex-wrap: wrap;

		.category_item {
			width: 33.33%;
			position: relative;
			border: 5rpx solid #fff;
			image {
				height: 240rpx;
			}

			.category_name {
				position: absolute;
				width: 100%;
				height: 50rpx;
				left: 0;
				bottom: 0;
				color: #fff;
				background-image: linear-gradient(to right top, rgba(0, 0, 0, 0));
				font-size: 40rpx;
				display: flex;
				align-items: center;
				padding-left: 20rpx;
			}
		}
	}
</style>
