<template>
	<view>
		传过来的id为：{{id}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				id:''
			};
		},
		onLoad(e){
			console.log(e)
			this.id=e.goods_id
		}
	}
</script>

<style lang="scss">

</style>
