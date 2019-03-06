<template>
	<view>
		my
	</view>
</template>

<script>
	export default {
		onLoad: function() {
			var loginRes = this.checkLogin('../my/my', '2');
			if (!loginRes) {
				return false;
			}
		},
		data() {
			return {
				
			};
		}
	}
</script>

<style>

</style>
