<template>
	<scroll-view class="ccontainer">
		<!-- banner 图模块-->
		<swiper class="swiper" :indicator-active-color="indicatorActiveColor" :indicator-color="indicatorColor"
		 :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration" :circular="circular">
			<swiper-item v-for="(value,index) in swiperList" :key="index">
				<image class="swiper-item" :src="imgUrl+value.advUrl" mode="" lazy-load="true"></image>
			</swiper-item>
		</swiper>
		<!-- 签到 -->
		<view class="qiandao main" @click="goQian">
			<image class="qiandao-img" :src="imgUrl+sign" mode="aspectFit" lazy-load="true"></image>
		</view>
		<!-- <view class="">
			<image :src="img" mode=""></image>
		</view> -->
		<!-- 399面膜 -->
		<view class="mianmo main" @click="goDetail(goodId)">
			<image class="mianmo-img" :src="imgUrl+goodsImg" mode="aspectFit" lazy-load="true"></image>
			<text class="mianmo-title">{{goodsName}}</text>
			<view class="mianmo-word">
				<text style="color: red;font-size: 40rpx;font-weight: 600;">￥ {{currentPice}}</text><text style="color: #B9B9B9;">已售{{sales}}件</text>
			</view>
		</view>
		<!-- 产品列表 -->
		<view class="lists main">
			<view class="product-item" v-for="(value,index) in goodsList" :key="index">
				<image class="product-img" :src="imgUrl+goodsImg" mode="" lazy-load="true" @click="goDetail(value.id)"></image>
				<view class="product-title">
					{{value.goodsName}}
				</view>
				<view class="product-price">
					<view class="product-price-num" v-if="value.payMethod==1">
						￥ {{value.currentPice}}
						<view class="product-sell-num">
							已售{{value.sales}}件
						</view>
					</view>
					<view class="product-price-num" v-if="value.payMethod==2">
						{{value.currentPice}} 积分
						<view class="product-sell-num">
							已兑换{{value.sales}}件
						</view>
					</view>
					<image class="product-icon-add" src="../../static/imgs/icon-add.png" mode="" @click="addCart(value.id)"></image>
				</view>
			</view>
		</view>
	</scroll-view>
</template>

<script>
	// 引入二维码库  
	import QR from "../../components/wxqrcode.js" // 二维码生成器
	var app = require("../../App.vue")
	export default {
		data() {
			return {
				indicatorDots: true,
				autoplay: true,
				interval: 3000,
				duration: 600,
				circular: true,
				indicatorColor: 'rgba(255, 255, 255, 0.2)',
				indicatorActiveColor: '#DBDBDB',
				swiperList: [], //轮播图
				imgUrl: app.default.globalData.imgUrl, //图片拼接位置
				sign: "", //签到图
				goodsImg: "", //主产品图
				goodsName:"",
				currentPice:"",
				sales:"",
				goodId: "", //主产品id
				goodsList: [], //首页商品列表
				page: 1,
				rows: 4,
				img:""
			}
		},
		onLoad() {
			console.log(app.default.globalData.openId)
			this.banner(); //轮播图
			this.signShow(); //签到图
			this.mainGood(); //主商品广告位
			this.indexGoods(this.page, this.rows) //主页商品列表
			this.img = QR.createQrCodeImg('生成的内容', {
				size: parseInt(300) //二维码大小  
			})
			
		},
		methods: {
			// 首页banner图
			banner: function() {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Home/AdvList",
					success(res) {
						if (res.data.code == 200) {
							that.swiperList = res.data.data
						}
					}
				})
			},
			// 主产品广告位
			mainGood: function() {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Home/GoodsObject",
					success(res) {
						console.log(res.data)
						if (res.data.code == 200) {
							that.goodsImg = res.data.data.goodsImg;
							that.goodId = res.data.data.id;
							that.goodsName = res.data.data.goodsName;
							that.currentPice =  res.data.data.currentPice;
							that.sales = res.data.data.sales;
						}
					}
				})
			},
			// 首页列表
			indexGoods: function(page, rows) {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Home/GoodsPage",
					data: {
						page: page,
						rows: rows
					},
					success(res) {
						// console.log("列表",JSON.stringify(res.data))
						if (res.data.code == 200) {
							that.goodsList = res.data.data.list
						}
					}
				})
			},
			goDetail(id) {
				uni.navigateTo({
					url: "../goods/goods?id=" + id
				})
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
						goodsId: id,
						Number: 1
					},
					success(res) {
						console.log(res.data)
						if (res.data.code == 200) {
							uni.showToast({
								duration: 1500,
								title: res.data.message,
								icon: "none"
							});
						}
					}
				})
			},
			// 签到
			signShow: function() {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Home/SignShow",
					success(res) {
						if (res.data.code == 200) {
							that.sign = res.data.data.advUrl
						}
					}
				})
			},
			goQian() {
				uni.navigateTo({
					url: "../mine/signin"
				})
			},
			onPullDownRefresh() {
				this.banner(); //轮播图
				this.signShow(); //签到图
				this.mainGood(); //主商品广告位
				this.indexGoods(this.page, this.rows) //主页商品列表
				uni.stopPullDownRefresh();
			},

		}
	}
</script>

<style>
	.swiper {
		height: 380upx;
	}

	.swiper-item {
		width: 100%;
		height: 380upx;
	}

	.qiandao {
		display: flex;
		align-self: center;
		/* padding: 20upx 5upx; */
	}

	.qiandao-img {
		width: 90%;
		height: 224upx;
		margin: 0upx auto;
	}

	.mianmo {
		/* padding: 0 46upx 30upx 46upx; */
		display: flex;
		flex-direction: column;
		margin-bottom: 30upx;
	}
	.mianmo-title {
		font-size: 30rpx;
		padding: 20rpx 20rpx;
		text-overflow: -o-ellipsis-lastline;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 1;
		line-clamp: 1;
		-webkit-box-orient: vertical;
	}

	.mianmo-img {
		width: 750upx;
		height: 750upx;
	}
	.mianmo-word {
		display: flex;
		justify-content: space-between;
		font-size: 24upx;
		padding: 20upx;
	}

	/* 产品列表 */
	.lists {
		padding: 0 32upx 30upx 46upx;
		display: flex;
		flex-flow: row wrap;
		justify-content: flex-start;
	}

	.product-item {
		width: 48%;
		box-sizing: border-box;
		margin: 0 2% 30upx 0;
	}

	.product-img {
		width: 100%;
		height: 326upx;
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
		line-height: 150%;
	}

	.product-title {
		line-height: 34upx;
		font-size: 28upx;
		text-overflow: -o-ellipsis-lastline;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 1;
		line-clamp: 1;
		-webkit-box-orient: vertical;
		margin-top: 10rpx;
	}

	.product-price-num {
		color: #FF0000;
		font-size: 30upx;

	}

	.product-sell-num {
		color: #B9B9B9;
		font-size: 23upx;
	}

	.product-icon-add {
		width: 36upx;
		height: 36upx;
	}
</style>
