<template>
	<scroll-view class="ccontainer">
		<!-- banner 图模块-->
		<swiper class="swiper" :indicator-active-color="indicatorActiveColor" :indicator-color="indicatorColor" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration" :circular="circular">
			<swiper-item v-for="(value,index) in swiperList" :key="index">
				<image class="swiper-item" :src="imgUrl+value.advUrl" mode="" lazy-load="true"></image>
			</swiper-item>
		</swiper>
		<!-- 签到 -->
		<view class="qiandao main" @click="goQian">
			<image class="qiandao-img" :src="imgUrl+sign" mode="" lazy-load="true" ></image>
		</view>
		<!-- 399面膜 -->
		<view class="mianmo main" @click=goDetail(goodId)>
			<image class="mianmo-img" :src="imgUrl+goodsImg" mode="" lazy-load="true"></image>
		</view>
		<!-- 产品列表 -->
		<view class="lists main">
			<view class="product-item" v-for="(value,index) in goodsList" :key="index" >
				<image class="product-img" :src="imgUrl+goodsImg" mode="" lazy-load="true" @click="goDetail(value.id)"></image>
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
	</scroll-view>
</template>

<script>
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
				swiperList:[],//轮播图
				imgUrl : app.default.globalData.imgUrl,//图片拼接位置
				sign:"",//签到图
				goodsImg:"",//主产品图
				goodId:"",//主产品id
				goodsList:[],//首页商品列表
				page:1,
				rows:4
			}
		},
		onLoad() {
			console.log(app.default.globalData.openId)
			this.banner();//轮播图
			this.signShow();//签到图
			this.mainGood();//主商品广告位
			this.indexGoods(this.page,this.rows)//主页商品列表
		},
		methods: {
			// 首页banner图
			banner:function() {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url:app.default.globalData.baseUrl+"/api/Home/AdvList",
					success(res) {
						if(res.data.code==200) {
							that.swiperList = res.data.data
						}	
					}	
				})
			},
			// 主产品广告位
			mainGood:function() {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url:app.default.globalData.baseUrl+"/api/Home/GoodsObject",
					success(res) {
						if(res.data.code==200) {
							that.goodsImg = res.data.data.goodsImg;
							that.goodId = res.data.data.id
						}	
					}	
				})
			},
			// 首页列表
			indexGoods:function(page,rows){
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url:app.default.globalData.baseUrl+"/api/Home/GoodsPage",
					data:{
						page:page,
						rows:rows
					},
					success(res) {
						// console.log("列表",JSON.stringify(res.data))
						if(res.data.code==200) {
							that.goodsList = res.data.data.list
						}	
					}	
				})
			},
			goDetail(id) {
				uni.navigateTo({
					url:"../goods/goods?id="+id
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
			// 签到
			signShow:function(){
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					method: "POST",
					dataType: 'json',
					url:app.default.globalData.baseUrl+"/api/Home/SignShow",
					success(res) {
						if(res.data.code==200) {
							that.sign = res.data.data.advUrl
						}	
					}	
				})
			},
			goQian() {
				uni.navigateTo({
					url:"../mine/signin"
				})
			},
			onPullDownRefresh() {
				this.banner();//轮播图
				this.signShow();//签到图
				this.mainGood();//主商品广告位
				this.indexGoods(this.page,this.rows)//主页商品列表
				uni.stopPullDownRefresh();
			},
			
		}
	}
</script>

<style>
.swiper{
	height:380upx;
}
.swiper-item{
	width: 100%;
	height:380upx;
}
.qiandao{
	padding: 30upx 46upx;
}
.qiandao-img{
	width: 100%;
	height: 210upx;
}
.mianmo{
	padding: 0 46upx 30upx 46upx;
}
.mianmo-img{
	width: 100%;
	height: 851upx;
}
/* 产品列表 */
.lists{
	padding: 0 32upx 30upx 46upx;
	display: flex;
	flex-flow: row wrap;
	justify-content: flex-start;
}
.product-item{
	width:48%;
	box-sizing: border-box;
	margin:0 2% 30upx 0;
}
.product-img{
	width: 100%;
	height: 326upx;
	display: flex;
	flex-flow: column nowrap;
	justify-content: center;
}
.product-price{
	padding: 0 12upx;
	box-sizing: border-box;
	display: flex;
	flex-flow: row nowrap;
	justify-content: space-between;
	align-items: center;
}
.product-title{
	line-height: 34upx;
	font-size: 28upx;
	padding: 12upx;
}
.product-price-num{
	color: #FF0000;
	font-size: 26upx;
	
}
.product-sell-num{
	color: #B9B9B9;
	font-size: 20upx;
}
.product-icon-add{
	width: 36upx;
	height: 36upx;
}
</style>
