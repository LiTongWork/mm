<template>
	<scroll-view class="ccontainer">
		<!-- 状态栏 -->
		<view class="status" :style="{position:headerPosition}"></view>
		<view class="header" :style="{position:headerPosition}">
			<view class="input-box">
				<image class="icon-search" src="/static/imgs/icon-search.png" mode="" @click="search()"></image>
				<input placeholder="请输入关键词" focus confirm-type="search" v-model="seachName" placeholder-style="color:#c0c0c0;"/>
			</view>
			<!-- <view class="cancel" @tap="back()">
				取消
			</view> -->
		</view>
		
		<view style="text-align: center;margin-top: 300rpx;" v-if="goodsList.length==0">
			<text>暂无商品哦~</text>
		</view>
		<!-- 产品列表 -->
		<view class="lists main" v-if="goodsList.length!=0">
			<view class="product-item" v-for="(value,index) in goodsList" :key="index">
				<image class="product-img" :src="imgUrl+value.goodsImg" mode="" lazy-load="true" @click="goDetil(value.id)"></image>
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
				showCategoryIndex: 0,
				headerPosition: "fixed",
				imgUrl: app.default.globalData.imgUrl, //图片拼接位置
				seachName: "",//搜索词
				goodsList:[],//商品列表
				page:1,
				rows:6
			}
		},
		onPageScroll(e) {
			//兼容iOS端下拉时顶部漂移
			if (e.scrollTop >= 0) {
				this.headerPosition = "fixed";
			} else {
				this.headerPosition = "absolute";
			}
		},
		onLoad() {
			console.log(this.goodsList, typeof this.goodsList)
		},
		methods: {
			//搜索商品
			search() {
				console.log(this.seachName);
				this.list(this.seachName,this.page,this.rows)
			},
			//跳转到详情
			goDetil(id) {
				uni.navigateTo({
					url:"../goods/goods?id="+id
				})
			},
			// 商品列表
			list:function(seachName,page,rows){
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						"auth": app.default.globalData.token
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Class/SearchGoods",
					data: {
						SeachName:seachName,
						page:page,
						rows:rows
					},
					success(res) {
						console.log(res.data);
						that.goodsList = that.goodsList.concat(res.data.data.list)
					}
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
			//取消返回上一页 
			back(){
				uni.navigateBack({
					delta: 1
				});
			},
			onPullDownRefresh() {
				this.list(this.seachName,this.page,this.rows)
			},
			onReachBottom() {
				let that = this;
				that.page++;
				that.list(that.seachName,that.page,that.rows)
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
			border:1upx solid #CBCBCB;
			border-radius: 50upx;
			position: relative;
			display: flex;
			align-items: center;
			.icon-search{
				width: 30upx;
				height: 32upx;
				margin:0 0 0 20upx;
			}
			input {
				padding-left: 28upx;
				height: 28upx;
				font-size: 28upx;
				width:95%;
			}
		}

		.cancel{
			width: 70upx;
			padding: 0 15upx;
			font-size: 28upx;
			color: #A2A2A2;
		}
	}

/* 产品列表 */
.lists{
	margin:150upx 0 0 0;
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
	width: 50upx;
	height: 50upx;
}
</style>
