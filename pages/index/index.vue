<template>
	<view>
		index
	</view>
</template>

<script>
	export default {
		onLoad: function() {
			uni.request({
				url: this.apiServer + 'index&m=index',
        method: 'GET',
        data: {},
        success: (res) => {
          console.log(res);
        }
			})
		},
		
	}
</script>

<style>
</style>
