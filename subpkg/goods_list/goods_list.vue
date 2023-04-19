<template>
	<view>
		{{query}}
		{{cid?'传过来的参数为：'+cid:''}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				query:'',
				cid:''
			};
		},
		onLoad(e){
			console.log(e)
			this.query=e.query,
			this.cid=e.cid
		}
	}
</script>

<style lang="scss">

</style>
