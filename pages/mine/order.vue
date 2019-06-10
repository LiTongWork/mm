<template>
	<view class="container">
		<!-- nav切换 -->
		<view class="header-nav">
			<view class="nav-list" :class="{active: status == item.status}" v-for="(item,index) in nav" :key='index' :data-status='item.status' @tap="changeStatus">
				<text>{{item.label}}</text>
			</view>
		</view>
		<!-- 商品列表 -->
		<view class="goods-list">
			<view class="list-item" v-for="(item,index) in goodsList" :key='index' :data-indentId='item.indentId'>
				<view class="number">
					<view class="">订单号：<text>{{item.indentCode}}</text></view>
					<view :data-status='item.indentStatus' v-if="item.indentStatus == 0">等待买家付款</view>
					<view :data-status='item.indentStatus' v-if="item.indentStatus == 1">等待卖家发货</view>
					<view :data-status='item.indentStatus' v-if="item.indentStatus == 2">等待收货</view>
					<view :data-status='item.indentStatus' v-if="item.indentStatus == 3 && !item.isReview ">待评价</view>
					<view :data-status='item.indentStatus' v-if="item.indentStatus == 3 && item.isReview ">已评价</view>
				</view>
				<view class="content dianpu" v-for="(ite,inde) in item.indentList" :key='inde'>
					<view class="shangpin" v-for="(it,ind) in ite.goodsList" :key='ind'>
						<view class="pic"><image :src="imgUrl + it.goodsImg"></image></view>
						<view class="desc">
							<view class="">
								<view class="title">
									<text>{{it.goodsName}}</text>
									<text class="count">x{{it.number}}</text>
								</view>
								<view class="freight">运费:{{it.freight > 0 ? it.freight : '免运费'}}</view>
							</view>
							<view class="price" v-if="item.payMethod == 1">￥{{it.money}}</view>
							<view class="price" v-if="item.payMethod == 2">{{it.money}}积分</view>
						</view>
					</view>
				</view>
				<view class="handle">
					<!-- <view >取消订单</view> -->
					<view class="black pay" v-if="item.indentStatus ==0" :data-paymethod='item.payMethod' :data-indentid='item.indentId' @tap="payAgain">付款</view>
					<view class="" @tap="toLogistics" v-if="item.indentStatus == 2" :data-indentid='item.indentId'>查看物流</view>
					<view class="black" @tap="confirmOrder" v-if="item.indentStatus == 2" :data-indentid='item.indentId'>确认收货</view>
					<view class="black evaluate" @tap="toEvaluate" v-if="item.indentStatus == 3" :data-status='item.indentStatus' :data-isreview='item.isReview' :data-indentid='item.indentId'>{{ item.isReview ? '查看评价' : '评价' }}</view>
				</view>
			</view>
			<view class="noData" v-if="goodsList.length == 0">暂无数据</view>
		</view>
	</view>
</template>

<script>
	const app = require('../../App.vue')
	export default {
		data(){
			return {
				imgUrl: app.default.globalData.imgUrl,
				nav: [
					{
						status: -1,
						label: '全部'
					},
					{
						status: 0,
						label: '待付款'
					},
					{
						status: 1,
						label: '待发货'
					},
					{
						status: 2,
						label: '待收货'
					},
					{
						status: 3,
						label: '待评价'
					}
				],
				goodsList: [],
				status: -1,
				page: 1,
				rows: 10
			}
		},
		onLoad(options) {
			let that = this;
			that.status = Number(options.status)
			console.log(that.status);
			that.getList();
		},
		onShow () {
			let that = this;
		},
		onPullDownRefresh() {
			console.log('refresh');
			let that = this;
			that.page = 1;
			that.goodsList = [];
			that.getList();
		},
		onReachBottom() {
			console.log('上拉触底');
			let that = this;
			that.page++;
			that.getList();
		},
		methods: {
			// nav 切换
			changeStatus (e) {
				let that = this;
				let status = e.currentTarget.dataset.status;
				console.log(status);
				if (that.status != status) {
					that.status = status;
					that.page = 1;
					that.goodsList = [];
					that.getList();					
				}
			},
			// 获取列表
			getList(){
				let that = this;
				uni.showLoading();
				let params = {
					page: that.page,
					rows: that.rows,
					status: that.status
				}
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/IndentHall",
					method: "POST",
					header: {
						'content-type': 'application/json',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: "json",
					success(res) {
						console.log(res.data.data.list);
						uni.hideLoading();
						uni.stopPullDownRefresh();
						if(res.data.code == 200) {
							that.goodsList = that.goodsList.concat(res.data.data.list)
						}
					},
					fail(res){
						console.log(res)
					}
				})					
			},
			// 重新下单
			payAgain (e) {
				let that = this;
				let payMethod = e.currentTarget.dataset.paymethod;
				let indentId = e.currentTarget.dataset.indentid;
				let params = {
					openId: app.default.globalData.openId,
					indentId: indentId
				}
				console.log(params)
				if (payMethod == 1) {
					// 人名币付款
					uni.request({
						url: app.default.globalData.baseUrl + '/api/User/AgainOrder',
						method: "POST",
						header: {
							'content-type': 'application/json',
							'auth': app.default.globalData.token
						},
						data: params,
						dataType: "json",
						success(res) {
							console.log(res.data.data.data);
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: res.data.data.data.timeStamp,
								nonceStr: res.data.data.data.nonceStr,
								package: res.data.data.data.prepayId,
								signType: 'MD5',
								paySign: res.data.data.data.sign,
								success: function (res) {
									console.log('success:' + JSON.stringify(res));
									that.page = 1;
									that.goodsList = [];
									that.getList();
								},
								fail: function (err) {
									console.log('fail:' + JSON.stringify(err));
								}
							});							
						},
						fail(res){
							console.log(res)
						}						
					})
				} else if (payMethod == 2) {
					// 积分付款
					uni.showModal({
						title: '提示',
						content: '是否重新积分兑换',
						success(res) {
							if (res.confirm) {
								console.log('用户点击确定');
								uni.request({
									url: app.default.globalData.baseUrl + '/api/User/IntegralAgainOrder',
									method: "POST",
									header: {
										'content-type': 'application/json',
										'auth': app.default.globalData.token
									},
									data: params,
									dataType: "json",
									success(res) {
										console.log(res);		
										uni.showToast({
											title: res.data.message,
											icon: 'none',
											mask: true,
											duration: 1500
										})
										if (res.data.code == 200) {
											that.page = 1;
											that.goodsList = [];
											that.getList();			
										}
										
									},
									fail(res){
										console.log(res)
									}						
								})									
							} else if (res.cancel) {
								console.log('用户点击取消');
							}							
						}
					})
				
				}
			},
			// 查看物流
			toLogistics (e) {
				console.log(e.currentTarget.dataset.indentid)
				let indentId = e.currentTarget.dataset.indentid;
				uni.navigateTo({
					url: `/pages/mine/logistics?indentId=${indentId}`
				})
			},
			// 确认收货
			confirmOrder (e) {
				let that = this;
				console.log(e.currentTarget.dataset.indentid);
				let indentId = e.currentTarget.dataset.indentid;
				let params = {
					indentId: indentId
				}
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/ConfirmTake",
					method: "POST",
					header: {
						'content-type': 'application/json',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: "json",
					success(res) {
						console.log(res);
						uni.showToast({
							title: res.data.message,
							icon: 'none',
							mask: true,
							duration: 1500
						})
						if (res.data.code == 200) {
							that.page = 1;
							that.goodsList = [];
							that.getList();
						}
					},
					fail(res){
						console.log(res)
					}
				})	
			},
			// 评价
			toEvaluate (e) {
				let indentId = e.currentTarget.dataset.indentid;
				let isReview = e.currentTarget.dataset.isreview;
				let status = e.currentTarget.dataset.status;
				uni.navigateTo({
					url: `/pages/mine/evaluate?indentId=${indentId}&isReview=${isReview}&status=${status}`,
					// url: 'pages/mine/index'
				})
			}
			
		}
	}
</script>

<style>
	.container {
		padding-top: 88upx;
	}
	.header-nav {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 88upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		background-color: #fff;
		border-bottom: 2px solid #fff;
	}
	.header-nav .nav-list {
		flex: 1;
		text-align: center;
	}
	.header-nav .nav-list text {
		display: inline-block;
		height: 88upx;
		line-height: 88upx;
		border-bottom: 2px solid #fff;
		font-size: 28upx;
	}
	.header-nav .nav-list.active text {
		border-bottom: 2px solid #313131;
	}
	/* 商品列表 */
	.goods-list {
		padding: 36upx 46upx;
	}
	.goods-list .list-item {
		box-sizing: border-box;
		background-color: #fff;
		border-radius: 10upx;
		padding: 20upx 12upx;
		font-size: 26upx;
		margin-bottom: 40upx;
	}
	.goods-list .list-item .number{
		display: flex;
		align-items: center;
		justify-content: space-between;
		line-height: 100upx;
	}
	.goods-list .list-item .content .shangpin {
		margin-bottom: 16upx;
		display: flex;
		justify-content: space-between;
	}
	.goods-list .list-item .content .pic {
		width: 180upx;
		height: 180upx;
	}
	.goods-list .list-item .content .pic image {
		width: 100%;
		height: 100%;
	}
	.goods-list .list-item .content .desc {
		flex: 1;
		padding: 0 10upx 0 20upx;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}
	.goods-list .list-item .content .desc .title {
		line-height: 40upx;
	}
	.goods-list .list-item .content .desc .title .count {
		padding-left: 20upx;
		color: #a2a2a2;
	}
	.goods-list .list-item .content .desc .freight {
		color: #a2a2a2;
		line-height: 50upx;
	}
	.goods-list .list-item .content .desc .price {
		color: #FF4719;
		font-size: 30upx;
	}
	.goods-list .list-item .handle {
		padding: 20upx 0;
		display: flex;
		justify-content: flex-end;
	}
	.goods-list .list-item .handle view {
		box-sizing: border-box;
		min-width: 150upx;
		height: 60upx;
		line-height: 58upx;
		border: 1px solid #e9e9e9;
		border-radius: 60upx;
		padding: 0 20upx;
		text-align: center;
		font-size: 24upx;
		margin-left: 20upx;
	}
	.goods-list .list-item .handle view.black {
		color: #fff;
		background-color: #313131;
	}
</style>
