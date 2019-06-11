<template>
	<view>
		<!-- 状态栏 -->
		<view class="status" :style="{position:headerPosition}"></view>
		<view class="header" :style="{position:headerPosition}">
			<view class="input-box" @click="toSearch()">
				<image class="icon-search" src="/static/imgs/icon-search.png" mode=""></image>
				<input placeholder="请输入关键词" placeholder-style="color:#c0c0c0;" />
			</view>
		</view>
		<!-- 占位 -->
		<view class="place"></view>
		<view class="category-list">
			<!-- 左侧分类导航 -->
			<scroll-view scroll-y="true" class="left">
				<view class="row" v-for="(category,index) in categoryList" :key="category.id" :class="[index==showCategoryIndex?'on':'']"
				 @tap="showCategory(index,category.id)">
					<view class="text">
						<view class="block"></view>
						{{category.dicName}}
					</view>
				</view>

			</scroll-view>
			<!-- 右侧子导航 -->
			<scroll-view scroll-y="true" class="right">
				<view class="category">
					<view class="list" v-for="(value,index) in categoryGoods" :key="index" v-show="index==showCategoryIndex">
						<view class="box">
							<view class="product-item">
								<image class="product-img" :src="imgUrl+value.goodsImg" mode="" lazy-load="true" @click="goDetail(value.id)"></image>
								<view class="product-title">
									{{value.goodsName}}
								</view>
								<view class="product-price">
									<view class="product-price-num" v-if="value.payMethod==1">
										{{value.currentPice}}元
										<view class="product-sell-num">
											已兑换{{value.sales}}件
										</view>
									</view>
									<view class="product-price-num" v-if="value.payMethod==2">
										{{value.currentPice}}积分
										<view class="product-sell-num">
											已兑换{{value.sales}}件
										</view>
									</view>
									<image class="product-icon-add" src="../../static/imgs/icon-add.png" mode="" @click="addCart(value.id)"></image>
								</view>
							</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	var app = require("../../App.vue")
	export default {
		data() {
			return {
				showCategoryIndex: 0,
				headerPosition: "fixed",
				imgUrl: app.default.globalData.imgUrl, //图片拼接位置
				//分类列表
				categoryList: [],
				categoryGoods: [], //分类商品列表
				GoodsTypeId: "", //切片对应的分类列表id
			}
		},
		// onPageScroll(e) {
		// 	//兼容iOS端下拉时顶部漂移
		// 	if (e.scrollTop >= 0) {
		// 		this.headerPosition = "fixed";
		// 	} else {
		// 		this.headerPosition = "absolute";
		// 	}
		// },
		onLoad() {
			this.category(0);
		},
		methods: {
			// 搜索商品
			toSearch() {
				uni.navigateTo({
					url:"search"
				})
			},
			//分类类别
			category: function(index) {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Class/GoodsClass",
					success(res) {
						if (res.data.code == 200) {
							that.categoryList = res.data.data;
							var id = res.data.data[index].id;
							console.log(id)
							that.changeGoodslist(id)
						}
					}
				})
			},
			changeGoodslist: function(id) {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Class/GoodsList",
					data: {
						GoodsTypeId: id
					},
					success(res) {
						console.log(res.data)
						if (res.data.code == 200) {
							for (var i = 0; i < res.data.data.length; i++) {
								that.categoryGoods=res.data.data
							}
							console.log(JSON.stringify(that.categoryGoods))

						}
					}
				})
			},
			//分类切换显示
			showCategory(index, id) {
				this.categoryList = []
				this.categoryGoods = []
				this.showCategoryIndex = index;
				this.category(index);
			},
			//加入购物车
			addCart(id) {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						"auth": app.default.globalData.token
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Store/AddCar",
					data: {
						goodsId:id,
						Number: 1
					},
					success(res) {
						console.log(res.data)
						if (res.data.code == 200) {
							uni.showToast({
								duration: 1500,
								title:res.data.message,
								icon: "none"
							});
						}
					}
				})
			},
			//跳转到详情页
			goDetail(id) {
				uni.navigateTo({
					url: '/pages/goods/goods?id=' + id
				});
			},
			onPullDownRefresh() {
				this.category(0);
				uni.stopPullDownRefresh();
			},
		}

	}
</script>

<style lang="scss">
	.status {
		width: 100%;
		height: 0;
		position: fixed;
		z-index: 10;
		background-color: #fff;
		top: 0;
		/*  #ifdef  APP-PLUS  */
		height: var(--status-bar-height); //覆盖样式
		/*  #endif  */

	}

	.header {
		width: 92%;
		padding: 0 4%;
		height: 100upx;
		display: flex;
		align-items: center;
		position: fixed;
		top: 0;
		z-index: 10;
		background-color: #fff;
		/*  #ifdef  APP-PLUS  */
		top: var(--status-bar-height);
		/*  #endif  */

		.input-box {
			width: 100%;
			height: 80upx;
			background-color: #FFF;
			border: 1upx solid #CBCBCB;
			border-radius: 40upx;
			position: relative;
			display: flex;
			align-items: center;

			.icon-search {
				width: 30upx;
				height: 32upx;
				margin: 0 0 0 20upx;
			}

			input {
				padding-left: 28upx;
				height: 28upx;
				font-size: 28upx;
			}
		}

		.cancel {
			width: 70upx;
			padding: 0 15upx;
			font-size: 28upx;
			color: #A2A2A2;
		}
	}

	.place {

		background-color: #ffffff;
		height: 100upx;
		/*  #ifdef  APP-PLUS  */
		margin-top: var(--status-bar-height);
		/*  #endif  */
	}

	.category-list {
		width: 100%;
		background-color: #fff;
		display: flex;
		padding: 20upx 0 0 0;
		background-color: #F8F8F8;

		.left,
		.right {
			position: absolute;

			top: 120upx;
			/*  #ifdef  APP-PLUS  */
			top: calc(100upx + var(--status-bar-height));
			/*  #endif  */
			bottom: 0upx;
		}

		.left {
			width: 24%;
			left: 0upx;
			background-color: #F8F8F8;

			.row {
				width: 100%;
				height: 90upx;
				display: flex;
				align-items: center;

				.text {
					width: 100%;
					position: relative;
					font-size: 28upx;
					display: flex;
					justify-content: center;
					color: #3c3c3c;

					.block {
						position: absolute;
						width: 0upx;
						left: 0;
					}
				}

				&.on {
					height: 100upx;
					background-color: #fff;

					.text {
						font-size: 30upx;
						font-weight: 600;
						color: #2d2d2d;

						.block {
							width: 4upx;
							height: 100upx;
							top: -30upx;
							background-color: #313131;
						}
					}
				}
			}
		}

		.right {
			width: 76%;
			left: 24%;
			padding: 30upx 0 0 0;

			.category {
				width: 100%;
				padding: 0 10upx 0 21upx;
				box-sizing: border-box;
				display: inline-flex;
				flex-wrap: wrap;

				.banner {
					width: 100%;
					height: 24.262vw;
					border-radius: 10upx;
					overflow: hidden;
					box-shadow: 0upx 5upx 20upx rgba(0, 0, 0, 0.3);

					image {
						width: 100%;
						height: 24.262vw;
					}
				}

				.list {
					// margin:0 0 40upx 0;
					width: 50%;
					display: flex;
					flex-wrap: wrap;

					.box {
						width: 100%;
						box-sizing: border-box;
						margin: 0 2% 0 0;

						.product-item {
							margin: 0 0 30upx 0;
						}

						.product-img {
							width: 100%;
							height: 248upx;
							display: flex;
							flex-flow: column nowrap;
							justify-content: center;
						}

						.product-price {
							padding: 0 12upx;
							box-sizing: border-box;
							display: flex;
							flex-flow: row nowrap;
							justify-content: space-between;
							align-items: center;
						}

						.product-title {
							line-height: 26upx;
							font-size: 20upx;
							padding: 12upx;
						}

						.product-price-num {
							color: #FF0000;
							font-size: 20upx;
						}

						.product-sell-num {
							color: #B9B9B9;
							font-size: 20upx;
						}

						.product-icon-add {
							width: 45upx;
							height: 45upx;
						}
					}
				}
			}
		}
	}
</style>
