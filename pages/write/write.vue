<template>
	<view>
		write
	</view>
</template>

<script>
	export default {
		onLoad: function() {
			var loginRes = this.checkLogin('../write/write', '2');
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
