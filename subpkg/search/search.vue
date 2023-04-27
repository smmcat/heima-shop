<template>
	<view class="content">

		<!-- 搜索栏目区域 -->
		<view class="search-box">
			<!-- 设置圆角宽度 输入事件 隐藏取消按钮 -->
			<uni-search-bar @input="input" :radius="100" :cancelButton="none"></uni-search-bar>
		</view>

		<!-- 搜索建议区域 -->
		<view class="sugg-list" v-if="searchResults.length !== 0">
			<view class="sugg-item" v-for="(item,index) in searchResults" :key="index"
				@click="gotoDetail(item.goods_id)">
				<view class="goods-name">{{item.goods_name}}</view>
				<!-- uni 提供的 Icon 图标 -->
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>

		<!-- 搜索历史区域 -->
		<view class="history-box" v-else>
			<!-- 搜索历史 标题 -->
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
			</view>
			<!-- 搜索历史 列表 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item,index) in historys" :key="index"
					@click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 初始化定时器
				timer: null,
				// 搜索关键词
				kw: '',
				// 搜索结果列表
				searchResults: [],
				// 搜索关键词历史记录
				historyList: []
			};
		},
		computed: {
			historys() {
				return [...this.historyList].reverse();
			}
		},
		methods: {
			// 输入事件处理函数
			input(e) {
				// 防抖操作 清除定时器
				if (this.timer) clearTimeout(this.timer);
				// 防抖操作 在规定时间无任何操作后 执行处理
				this.timer = setTimeout(() => {
					this.kw = e.trim();
					// 发起请求
					this.getSearchList();
				}, 500);
			},
			// 根据搜索关键词 获取搜索商品建议列表
			async getSearchList() {
				// 如果用户搜索框为空
				if (this.kw === '') {
					this.searchResults = [];
					return
				}
				// 发起请求 获取对应数据
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/qsearch', {
					query: this.kw
				});
				// 请求失败 返回 弹出提示
				if (res.meta.status !== 200) return uni.$showMsg();
				// 请求成功 进行赋值
				this.searchResults = res.message;
				// 查询到结果后 保存当前搜索关键词 到 搜索历史列表中
				this.saveSearchHistory();
			},
			// 跳转路由处理函数 传入 商品参数
			gotoDetail(goods_id) {
				// 跳转到商品详情页面 并传递 goods_id 参数
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
				})
			},
			// 保存历史记录 并 持久化存储
			saveSearchHistory() {

				// // 创建 set 对象为 historyList 去重
				// const set = new Set(this.historyList);
				// set.delete(this.kw);
				// set.add(this.kw);
				// // 将 set 对象 转为数组并赋值到 historyList
				// this.historyList=Array.from(set);
				// // 将更新的 历史记录 存储在本地
				// uni.setStorageSync('kw',JSON.stringify(this.historyList));
				// includes() 判断搜索关键字是否已经存在于搜索历史列表


				if (!this.historyList.includes(this.kw)) {
					// 如果不存在 则将其添加到 数组中
					this.historyList.push(this.kw)
					// 将更新的 历史记录 存储在本地
					uni.setStorageSync('kw', JSON.stringify(this.historyList));
				} else {
					// 如果存在相同内容 获取其下标
					const index = this.historyList.indexOf(this.kw);
					// 删除该下标的成员
					this.historyList.splice(index, 1);
					// 历史记录列表中 插入 当前搜索的关键词 [置尾操作]
					this.historyList.push(this.kw);
					// 将更新的 历史记录 存储在本地
					uni.setStorageSync('kw', JSON.stringify(this.historyList));
				};
			},
			// 清除 历史记录数据
			cleanHistory() {
				// 初始化 历史记录
				this.historyList = [];
				// 初始化 本地存储的历史记录
				uni.setStorageSync('kw', '[]');
			},
			gotoGoodsList(kw) {
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?query=' + kw
				})
			}
		},
		onLoad() {
			// 将本地存储的历史记录 赋值
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]');
		}
	}
</script>

<style lang="scss">
	body {
		background-color: #ffffff;

		.search-box {
			position: sticky;
			top: 0;
			z-index: 999;
		}

		.uni-searchbar {
			display: flex;
			flex-direction: row;
			position: relative;
			padding: 15rpx;
			background-color: #c00000;
		}

		.sugg-list {
			padding: 0 5px;

			.sugg-item {
				font-size: 12px;
				padding: 13px 0;
				border-bottom: 1px solid #efefef;
				display: flex;
				align-items: center;
				justify-content: space-between;
			}

			.goods-name {
				white-space: nowrap;
				// 溢出部分隐藏
				overflow: hidden;
				// 文本溢出后，使用 ... 代替
				text-overflow: ellipsis;
				margin-right: 3px;
			}
		}

		.history-box {
			padding: 0 5px;

			.history-title {
				display: flex;
				justify-content: space-between;
				align-items: center;
				height: 40px;
				font-size: 13px;
				border-bottom: 1px solid #efefef;
			}

			.history-list {
				display: flex;
				flex-wrap: wrap;
			}

			uni-tag {
				margin-top: 10px;
				margin-right: 5px;
			}

			.uni-tag {
				background-color: #efefef;
				border: 0;
				color: #000;
			}
		}
	}
</style>