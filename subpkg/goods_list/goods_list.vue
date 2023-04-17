<template>
	<view>
		{{query}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				query:''
			};
		},
		onLoad(e){
			console.log(e)
			this.query=e.query
		}
	}
</script>

<style lang="scss">

</style>
