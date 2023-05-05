<template>
	<view class="goods-item">
		<!-- 商品左侧图片区域 -->
		<view class="goods-item-left">
			<image :src="item.goods_small_logo || defaultPic" mode="widthFix" class="goods-pic"></image>
		</view>
		<!-- 商品右侧信息区域 -->
		<view class="goods-item-right">
			<!-- 商品标题 -->
			<view class="goods-name">{{item.goods_name}}</view>
			<view class="goods-info-box">
				<!-- 商品价格 -->
				<view class="goods-price">￥{{item.goods_price | tofixed}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: "my-goods",
		data() {
			return {
				defaultPic: 'http://smmcat.cn/wp-content/uploads/2021/09/4557fb13e7d2a0e0.jpg'
			};
		},
		props: {
			item: {
				type: Object,
				default: {}
			}
		},
		filters: {
			tofixed(num) {
				return Number(num).toFixed(2);
			}
		}
	}
</script>

<style lang="scss">
	body {
		background-color: #fff;
	}

	.goods-item {
		display: flex;
		padding: 10px 5px;
		border-bottom: 1px solid #f0f0f0;

		.goods-item-left {
			margin-right: 5px;
		}

		.goods-pic {
			width: 100px;
			height: 100px;
			display: block;
		}

		.goods-item-right {
			display: flex;
			flex-direction: column;
			justify-content: space-between;

			.goods-name {
				font-size: 13px;
			}

			.goods-price {
				font-size: 16px;
				color: #c00000;
			}
		}
	}
</style>