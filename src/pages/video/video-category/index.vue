<template>
	<view>5</view>
</template>

<script>
	export default {
		props: {
			url: String
		},
		mounted() {
			console.log(this.url)
			this.getList()
		},
		methods: {
			getList() {
				this.request({
					url: this.url
				}).then(result => {
					console.log(result)
				})
			}
		}
	}
</script>

<style>
</style>
