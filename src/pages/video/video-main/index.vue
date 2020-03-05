<template>
	<view>1-4</view>
</template>

<script>
	export default {
		props: {
			urlobj: Object
		},
		data() {
			return {
			}
		},
		watch:{
			urlobj(){
				console.log(this.urlobj)
				this.getList()
			}
		},
		mounted() {
			console.log(this.urlobj)
			this.getList()
		},
		methods:{
			getList(){
				this.request({
					url:this.urlobj.url,
					data:this.urlobj.params
				}).then(result=>{
					console.log(result)
				})
			}
		}
	}
</script>

<style>
</style>
