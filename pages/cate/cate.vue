<template>
	<view>
		<my-search @click='gotoSearch'></my-search>
		<view class="scroll-view-container">
			<!-- 左侧的滚动视图区域 -->
			<scroll-view class="left-scroll-view" scroll-y="true" :style="{height:wh + 'px'}">
				<block v-for="(item,index) in cateList" :key="index">
					<view :class="['left-scroll-view-item',index === active ? 'active' : '']"
						@click="activeChaged(index)">
						{{item.cat_name}}
					</view>
				</block>
			</scroll-view>
			<!-- 右侧的滚动视图区域 -->
			<scroll-view class="right-scroll-view" scroll-y="true" :style="{height:wh + 'px'}" :scroll-top="scrollTop">
				<view class="cate-lv2" v-for="(item,index) in cateLevel2" :key="index">
					<!-- 二级分类 标题 -->
					<view class="cate-lv2-title">/ {{item.cat_name}} /</view>
					<!-- 三级分类列表数据 -->
					<view class="cate-lv3-list">
						<!-- 二级分类 Item 项 -->
						<view class="cate-lv3-item" v-for="(item_2,index_2) in item.children" :key="index_2"
							@click="gotoGoodsList(item_2)">
							<!-- Item 项 图标图片 -->
							<image :src="item_2.cat_icon"></image>
							<!-- Item 项 图标文本 -->
							<text>{{item_2.cat_name}}</text>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				wh: 0,
				cateList: [],
				cateLevel2: [],
				active: 0,
				scrollTop: 0
			};
		},
		onLoad() {
			// 获取设备信息 并将内容区域高度赋值
			const info = uni.getSystemInfoSync();
			this.wh = info.windowHeight - 50;

			// 调用分类列表数据
			this.getCateList();
		},
		methods: {
			async getCateList() {
				// 发起请求 获取数据
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/categories');
				// 判断是否请求失败
				if (res.meta.status !== 200) return uni.$showMsg();
				// 赋值数据
				this.cateList = res.message;
				// 为 二级分类 赋值
				this.cateLevel2 = res.message[0].children;

			},
			// 用户点击左侧一级列表后 事件处理函数
			activeChaged(index) {
				this.active = index;
				// 重新为 二级分类 赋值
				this.cateLevel2 = this.cateList[index].children;
				// 让 scrollTop 的值在 0 与 1 之间切换
				this.scrollTop = this.scrollTop ? 0 : 1;
			},
			gotoGoodsList(item) {
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?cid=' + item.cat_id
				})
			},
			gotoSearch() {
				uni.navigateTo({
					url:'/subpkg/search/search'
				})
			}
		}
	}
</script>

<style lang="scss">
	.scroll-view-container {
		display: flex;

		.left-scroll-view {
			width: 120px;

			.left-scroll-view-item {
				position: relative;
				background-color: #f7f7f7;
				line-height: 60px;
				text-align: center;
				font-size: 12px;

				&.active {
					background-color: #ffffff;

					&::before {
						position: absolute;
						content: ' ';
						display: block;
						width: 3px;
						height: 40px;
						top: 50%;
						left: 0;
						transform: translateY(-50%);
						background-color: #c00000;
					}
				}
			}

		}

		.right-scroll-view {
			background-color: #ffffff;
			flex: 1;

			.cate-lv2-title {
				font-size: 12px;
				font-weight: bold;
				text-align: center;
				padding: 15px 0;
			}

			.cate-lv3-list {
				display: flex;
				flex-wrap: wrap;

				.cate-lv3-item {
					width: 33.33%;
					margin-bottom: 10px;
					display: flex;
					flex-direction: column;
					align-items: center;

					image {
						width: 60px;
						height: 60px;
					}

					text {
						font-size: 12px;
					}
				}
			}
		}
	}
</style>