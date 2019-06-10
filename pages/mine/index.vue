<template>
	<view class="container">
		<!-- 头像昵称等 -->
		<view class="header">
			<image class="bg" src="/static/imgs/mine-bg.png" ></image>
			<view class="info">
				<view class="avatar">
					<image :src='userInfo.headImg ? userInfo.headImg : "/static/imgs/default.png" ' ></image>
				</view>
				<view class="nickName">
					<text class="text">{{userInfo.nickName}}</text>
					<view class="level">V{{userInfo.level}}</view>
				</view>
				<!-- <view class=""><text>ID: {{userInfo.id}}</text></view> -->
				<view class="referees"><text>推荐人: {{userInfo.driveNickName ? userInfo.driveNickName : '无'}}</text></view>
			</view>
		</view>
		<!-- 余额积分 -->
		<view class="money">
			<view class="money-item balance">
				<view class="text">￥{{userInfo.surplus}}</view>
				<view class="desc">我的余额</view>
			</view>
			<view class="line"></view>
			<view class="money-item integral">
				<view class="text">{{userInfo.integral}}</view>
				<view class="desc">我的积分</view>
			</view>
		</view>
		<!-- 商城订单 -->
		<view class="order">
			<view class="title" data-status='-1' @tap="toOrder">
				<view class="text">商城订单</view>
				<view class="all"><text>查看全部订单</text><uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon></view>
			</view>
			<view class="order-list">
				<view class="list-item" v-for="(item,index) in orderList" :key='index' :data-status='index' @tap="toOrder">
					<view class="icon">
						<view class="icon-box">
							<image :src="item.icon"></image>
							<view class="number" v-if="item.number > 0">{{item.number}}</view>
						</view>						
					</view>
					<view class="text">{{item.label}}</view>
				</view>
			</view>
		</view>
		<!-- 列表 -->
		<view class="list">
			<view class="list-item wallet" @tap="toWallet">
				<view class="left">
					<image src="/static/imgs/icon-wallet.png" ></image>
					<text>我的钱包</text>
				</view>
				<uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon>
			</view>
			<view class="list-item signin" @tap="toSignin">
				<view class="left">
					<view>
						<image src="/static/imgs/icon-signin.png" ></image>
					</view>
					<text>我的签到</text>
				</view>
				<uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon>
			</view>
			<view class="list-item address" @tap="toAddress">
				<view class="left">
					<image src="/static/imgs/icon-address.png" ></image>
					<text>收货地址</text>
				</view>
				<uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon>
			</view>
			<view class="list-item share" @tap="toShare">
				<view class="left">
					<image src="/static/imgs/icon-share.png" ></image>
					<text>我的分享</text>
				</view>
				<uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon>
			</view>
			<view class="list-item fenxiao" @tap="toFenxiao">
				<view class="left">
					<image src="/static/imgs/icon-fenxiao.png" ></image>
					<text>分销中心</text>
				</view>
				<uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon>
			</view>			
		</view>
	</view>
</template>

<script>
	const app = require("../../App.vue");
	import uniIcon from "@/components/uni-icon/uni-icon.vue"
	export default {
		components: {uniIcon},
		data(){
			return {
				userInfo: {},
				orderList: [
					{
						icon: '/static/imgs/icon-pay.png',
						label: '待付款',
						number: 0
					},
					{
						icon: '/static/imgs/icon-send.png',
						label: '待发货',
						number: 0
					},
					{
						icon: '/static/imgs/icon-goods.png',
						label: '待收货',
						number: 0
					},
					{
						icon: '/static/imgs/icon-evaluation.png',
						label: '待评价',
						number: 0
					}
				]
			}
		},
		onShow(){
			let that = this;
			that.getUserInfo();
			that.getOrder();
		},
		methods: {
			// 获取个人信息
			getUserInfo(){
				let that = this;
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/MyLoginInfo",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					dataType: "json",
					success(res) {
						// console.log(res);
						if(res.data.code == 200) {
							res.data.data.headImg = JSON.parse(res.data.data.headImg);
							res.data.data.nickName = JSON.parse(res.data.data.nickName);
							that.userInfo = res.data.data;
						}
					},
					fail(res){
						console.log(res)
					}
				})
			},
			// 获取订单数目
			getOrder(){
				let that = this;
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/IndentStatis",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					dataType: "json",
					success(res) {
						// console.log(res);
						if(res.data.code == 200) {
							that.orderList[0].number = res.data.data.noPay;
							that.orderList[1].number = res.data.data.pay;
							that.orderList[2].number = res.data.data.go;
							that.orderList[3].number = res.data.data.collect;
						}
					},
					fail(res){
						console.log(res)
					}
				})
			},
			//  跳转
			toOrder (e) {
				// 跳转订单列表
				let status = e.currentTarget.dataset.status;
				// console.log(status);
				uni.navigateTo({
					url: `/pages/mine/order?status=${status}`
				})
			},
			toWallet (e) {
				// 跳转我的钱包
				uni.navigateTo({
					url: '/pages/mine/wallet'
				})
			},
			toSignin(e) {
				// 跳转我的签到
				let that = this;
				uni.navigateTo({
					url: `/pages/mine/signin?integral=${that.userInfo.integral}`
				})
			},
			toAddress(e) {
				// 跳转我的地址
				uni.navigateTo({
					url: '/pages/mine/address'
				})
			},
			toShare(e){
				// 我的分享
				uni.navigateTo({
					url:'/pages/mine/share'
				})
			},
			toFenxiao(e){
				// 分销中心
				uni.navigateTo({
					url: '/pages/fenxiao/index'
				})
			}
		}
	}
	
</script>

<style>
	.container {
		background-color: #f8f8f8;
	}
	/* 头像昵称等信息 */
	.header {
		position: relative;
		height: 410upx;
		background-color: transparent;
	}
	.header image.bg {
		position: absolute;
		left: 0;
		top: 0;
		z-index: 0;
		width: 100%;
		height: 100%;
	}
	.header .info {
		position: absolute;
		z-index: 1;
		width: 100%;
		height: 100%;		
		color: #fff;
		font-size: 28upx;
		text-align: center;
		line-height: 40upx;
	}
	.info .avatar {
		display: flex;
		justify-content: center;
	}
	.info .avatar image {
		margin-top: 50upx;
		width: 150upx;
		height: 150upx;
		border-radius: 50%;
	}
	.info .nickName {
		color: #fff;
		height: 60upx;
		line-height: 60upx;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.info .nickName .text {
		font-size: 30upx;
		margin-right: 10upx;
	}
	.info .nickName .level {
		height: 30upx;
		line-height: 30upx;
		font-size: 20upx;
		padding: 0 16upx;
		background-color: #e9cd9b;
		border-radius: 30upx;
	}
	/* 余额积分 */
	.money {
		margin: 0 46upx;
		height: 180upx;
		background-color: #fff;
		border-radius: 10upx;
		position: relative;
		left: 0;
		top: -50upx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.money .line {
		width: 1px;
		height: 88upx;
		background-color: #e9e9e9;
		margin: 0 80upx;
	}
	.money .money-item {
		font-size: 26upx;
		text-align: center;
	}
	.money .money-item .text {
		font-size: 30upx;
		font-weight: bold;
		line-height: 50upx;
	}
	.money .money-item .desc {
		line-height: 40upx;
	}
	/* 商城订单 */
	.order {
		box-sizing: border-box;
		background-color: #fff;
		padding: 0 36upx 0 46upx;
	}
	.order .title {
		height: 80upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.order .title .text {
		font-size: 30upx;
		font-weight: bold;
	}
	.order .title .all {
		color: #a2a2a2;
		font-size: 24upx;
		display: flex;
		align-items: center;
	}
	.order .order-list {
		padding:10upx 12upx 26upx 0;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.order .order-list .list-item .icon {
		height: 40upx;
	}
	.order .order-list .list-item .icon .icon-box {
		position: relative;
		margin: 0 auto;
		width: 40upx;
		height: 40upx;
	}
	.order .order-list .list-item .icon .icon-box image {
		width: 100%;
		height: 100%;
	}
	.order .order-list .list-item .icon .icon-box .number {
		position: absolute;
		top: -14upx;
		left: 24upx;
		min-width: 30upx;
		height: 30upx;
		line-height: 30upx;
		text-align: center;
		font-size: 20upx;
		color: #fff;
		background-color: #e9cd9b;
		border-radius: 30upx;
	}
	.order .order-list .list-item .text {
		line-height: 46upx;
		font-size: 26upx;
	}
	
	/* 列表 */
	.list {
		margin-top: 30upx;
		font-size: 26upx;
		padding-bottom: 30upx;
	}
	.list .list-item {
		padding: 0 36upx 0 46upx;
		height: 100upx;
		background-color: #fff;
		border-bottom: 1px solid #e9e9e9;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.list .list-item .left {
		display: flex;
		align-items: center;
	}
	.list .list-item .left image {
		margin-right: 20upx;
		width: 46upx;
		height: 46upx;
	}
</style>
