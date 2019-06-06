<template>
	<view class="container">
		<view class="head">
			<image class="bg" src="/static/imgs/mine-bg.png"></image>
			<view class="content">
				<view class="icon"><image src="/static/imgs/gold.png"></image></view>
				<view class="money">￥{{userInfo.surplus}}</view>
			</view>
		</view>
		<view class="tx">
			<view class="input"><input type="number" v-model="txMoney" placeholder="输入提现金额"></view>
			<view class="tip">{{tip}}</view>
		</view>
		<view class="handle"><button @tap="tx">申请提现</button></view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		data(){
			return {
				userInfo: {},
				txMoney: '',
				tip: '*提取金额以100整数提取，每提取一次收取20%平台费'
			}
		},
		onLoad(){
			let that = this;
			that.getUserInfo();
		},
		onPullDownRefresh(){
			let that = this;
			that.getUserInfo();
		},
		methods: {
			// 获取个人信息
			getUserInfo(){
				let that = this;
				uni.showLoading()
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/MyLoginInfo",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					dataType: "json",
					success(res) {
						console.log(res);
						uni.hideLoading();
						uni.stopPullDownRefresh();
						if(res.data.code == 200) {
							res.data.data.surplus = res.data.data.surplus.toFixed(2);
							that.userInfo = res.data.data;
						}
					},
					fail(res){
						console.log(res)
					}
				})
			},
			// 提现
			tx(){
				let that = this;
				console.log(that.txMoney,app.default.globalData.openId,Number(that.userInfo.surplus));
				if(that.txMoney == '') {
					uni.showToast({
						title: '请输入提现金额',
						icon: 'none',
						mask: true,
						duration: 1500
					})
				} else if (that.txMoney == 0){
					uni.showToast({
						title: '提现金额不能为零',
						icon: 'none',
						mask: true,
						duration: 1500
					})
				} else if (that.txMoney > Number(that.userInfo.surplus) ) {
					uni.showToast({
						title: '提现金额不能大于佣金总额',
						icon: 'none',
						mask: true,
						duration: 1500
					})					
				} else if ( (that.txMoney) % 100 != 0 ) {
					uni.showToast({
						title: '提现金额必须为100的整数倍',
						icon: 'none',
						mask: true,
						duration: 1500
					})						
				} else if (app.default.globalData.openId == '') {
					uni.showToast({
						title: 'openId为空，请关闭小程序重新打开',
						icon: 'none',
						mask: true,
						duration: 1500
					})						
				} else {
					let params = {
						money: that.txMoney,
						openId: app.default.globalData.openId
					}
					uni.request({
						url: app.default.globalData.baseUrl + "/api/Distribution/Deposit",
						method: "POST",
						header: {
							'content-type': 'application/x-www-form-urlencoded',
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
								uni.navigateBack()
							}
						},
						fail(res){
							console.log(res)
						}
					})					
				}
			}
		}
		
	}
</script>

<style>
	.head {
		position: relative;
		width: 100%;
		height: 383upx;
		background-color: transparent;
	}
	.head .bg {
		position: absolute;
		left: 0;
		top: 0;
		z-index: 0;
		width: 100%;
		height: 100%;
	}
	.head .content {
		position: relative;
		width: 100%;
		height: 100%;
		z-index: 1;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.head .content .icon, .head .content .icon image{
		width: 290upx;
		height: 184upx;
	}
	.head .content .money {
		font-size: 46upx;
		color: #E9CD9B;
	}
	/* 提现金额 */
	.tx {
		margin-top: 100upx;
	}
	.tx .input input {
		box-sizing: border-box;
		margin: 0 46upx;
		padding: 10upx 20upx;
		height: 100upx;
		font-size: 28upx;
		border: 1px solid #e9e9e9;
		border-radius: 10upx;
	}
	.tx .tip {
		margin: 20upx 46upx;
		font-size: 24upx;
		color: #a2a2a2;
		line-height: 40upx;
	}
	/* 申请提现 */
	.handle {
		margin: 100upx 0;
	}
	.handle button {
		margin: 0 46upx;
		color: #fff;
		background-color: #313131;
	}
</style>
