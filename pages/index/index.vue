<template>
	<view>
		index
	</view>
</template>

<script>
	export default {
		onLoad: function() {
			var loginRes = this.checkLogin('../index/index', '2');
			if (!loginRes) {
				return false;
			}
		},
		
	}
</script>

<style>
</style>
