<template>
	<view>
		<!-- 轮播图区域 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<!-- 循环轮播图的 item 项 -->
			<swiper-item v-for="(item,index) in swiperList" :key="index">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<!-- 轮播图 图片 -->
					<image :src="item.image_src" mode="widthFix"></image>
				</navigator>
			</swiper-item>
		</swiper>

		<!-- 分类导航区域 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item,index) in newList" :key="index" @click="navClickHandler(item)">
				<image :src="item.image_src" mode="widthFix" class="nav-img"></image>
			</view>
		</view>

		<!-- 楼层区域 -->
		<view class="floor-list">
			<!-- 每个楼层的 item 项 -->
			<view class="floor-item" v-for="(item,index) in floorList" :key="index">
				<image :src="item.floor_title.image_src" mode="widthFix" class="floor-title"></image>
				<!-- 楼层的 图片区域 -->
				<view class="floor-img-box">
					<!-- 左侧 大图 -->
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src"
							:style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
					</navigator>

					<!-- 右侧 小图 -->
					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item_2,index_2) in item.product_list" :key="index_2"
							:url="item_2.url" v-if="index_2 !== 0">
							<image :src="item_2.image_src" mode="widthFix" :style="{width: item_2.image_width + 'rpx'}">
							</image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 轮播图数据列表
				swiperList: [],
				newList: [],
				floorList: []
			};
		},
		methods: {
			// 获取轮播图数据
			async getSwiperList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/swiperdata');
				if (res.meta.status !== 200) return uni.$showMsg();
				this.swiperList = res.message
			},
			async getNavList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/catitems');
				if (res.meta.status !== 200) return uni.$showMsg();
				this.newList = res.message;
			},
			async getFloorList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/floordata');
				if (res.meta.status !== 200) return uni.$showMsg();

				/*
				   接口提供的 navigator_url 不符合我们项目的 路由路径 [需要进行修改]
				   通过双层 forEach 循环 处理 URL 地址
				*/
				res.message.forEach(floor => {
					floor.product_list.forEach(prod => {
						prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
					})
				});

				// 处理后的结果 赋值到 floorList
				this.floorList = res.message;

			},
			navClickHandler(item) {
				if (item.name === '分类') {
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}
			}
		},
		onLoad() {
			// 调用 获取轮播图数据方法
			this.getSwiperList();
			// 调用 获取分类数据方法
			this.getNavList();
			// 调用 楼层数据方法
			this.getFloorList();
		}
	}
</script>

<style lang="scss">
	swiper {
		height: 330rpx;
	}

	.swiper-item,
	image {
		width: 100%;
		height: 100%;
	}

	.nav-list {
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		margin: 15rpx 0;
	}

	.nav-img {
		width: 128rpx;
		height: 140rpx;
	}

	.floor-title {
		height: 60rpx;
		width: 100%;
	}

	.right-img-box {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}

	.floor-img-box {
		display: flex;
		padding-left: 10rpx;
	}
</style>