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
			<view class="list-item" v-for="(item,index) in goodsList" :key='index'>
				<view class="number">
					<view class="">订单号：<text>{{item.number}}</text></view>
					<view>等待买家付款</view>
				</view>
				<view class="content">
					<view class="pic"><image :src="item.img"></image></view>
					<view class="desc">
						<view class="">
							<view class="title">
								<text>{{item.title}}</text>
								<text class="count">x{{item.count}}</text>
							</view>
							<view class="freight">运费:{{item.freight > 0 ? item.freight : '免运费'}}</view>
						</view>
						<view class="price">￥{{item.price}}</view>
					</view>
				</view>
				<view class="handle">
					<view >取消订单</view>
					<view class="black">付款</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data(){
			return {
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
				status: -1,
				goodsList: [
					{
						number: '1234567890',
						img: '/static/imgs/product.jpg',
						title: '玻尿酸多效保湿蚕丝男女士免洗睡眠面膜补水保湿清洁收缩毛孔',
						count: 1,
						freight: 0,
						price: 399
					},
					{
						number: '1234567890',
						img: '/static/imgs/product.jpg',
						title: '玻尿酸多效保湿蚕丝男女士免洗睡眠面膜补水保湿清洁收缩毛孔',
						count: 1,
						freight: 0,
						price: 399
					}
				]
			}
		},
		onLoad(options) {
			let that = this;
			that.status = Number(options.status)
			console.log(that.status)
		},
		onPullDownRefresh() {
			console.log('refresh');
			setTimeout(function () {
				uni.stopPullDownRefresh();
			}, 1000);
		},
		onReachBottom() {
			console.log('上拉触底')
		},
		methods: {
			// nav 切换
			changeStatus (e) {
				let that = this;
				let status = e.currentTarget.dataset.status;
				console.log(status);
				that.status = status;
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
	.goods-list .list-item .content {
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
