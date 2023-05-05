<template>
	<view>
		<view class="goods-list">
			<view v-for="(item,index) in goodsList" :key="index" @click="gotoDetail(item)">
				<my-goods :item='item'></my-goods>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 请求参数
				queryObj: {
					// 查询关键词
					query: '',
					// 商品分类 id
					cid: '',
					// 页码值
					pagenum: 1,
					// 每页显示数量
					pagesize: 10
				},
				goodsList: [],
				total: 0,
				isloading: false
			};
		},
		onLoad(options) {
			// 获取传入的参数 并赋值到 queryObj 对应的对象中
			this.queryObj.query = options.query || '';
			this.queryObj.cid = options.cid || '';

			// 调用获取商品列表方法
			this.getGoodsList();
		},
		methods: {
			// 获取商品列表数据
			async getGoodsList(callBack) {
				// 节流阀 开
				this.isloading = true;
				// 发起网络请求
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/search', this.queryObj);
				// 如果获取失败 返回错误提示
				if (res.meta.status !== 200) return uni.showMsg();
				// 进行赋值操作 并与旧数据拼接
				this.goodsList = [...this.goodsList, ...res.message.goods];
				this.total = res.message.total;
				// 节流阀 关
				this.isloading = false;
				// 如有传入回调函数 执行回调函数
				callBack && callBack();
			},
			gotoDetail(item) {
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
				})
			}
		},
		// 上拉触底事件
		onReachBottom() {
			// 当上个请求还没响应 不进行新的请求操作
			if (this.isloading) return;
			// 当请求的数据值大于服务器提供的所有值 不再进行操作并提示
			if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('没有更多内容了');
			// 页码值 + 1
			this.queryObj.pagenum += 1;
			// 重新请求数据
			this.getGoodsList();
		},
		// 下拉刷新事件
		onPullDownRefresh() {
			// 重置关键值
			this.goodsList = [];
			this.isloading = false;
			this.total = 0;
			this.queryObj.pagenum = 1;

			this.getGoodsList(() => uni.stopPullDownRefresh());
		}
	}
</script>

<style lang="scss">

</style>