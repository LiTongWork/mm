<template>
	<view class="container">
		<image class="bg1" src="/static/imgs/signin-bg1.jpg"></image>
		<view class="info">
			<view class="desc">签到满5天，可得面膜</view>
			<view class="content">
				<image class="bg2" src="/static/imgs/signin-bg2.png"></image>
				<view class="integral">
					<view class="score">{{integral}}</view>
					<view class="text">我的积分</view>
				</view>
				<view class="signDays"><text>{{signDays}}</text>天</view>
				<view class="signStatus">
					<view class="signStatus-item" v-for="(item,index) in 7" :key='index'>
						<image v-if="signDays >= (item + 1)" src="/static/imgs/signStatus2.png"></image>
						<image v-else src="/static/imgs/signStatus1.png"></image>
						<view>第{{item + 1}}天</view>
					</view>
				</view>
				<view class="handle">
					<button v-if="!hasSign" class="hasSign" @tap="signin">立即签到</button>
					<button v-else disabled="true">已签到</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		data(){
			return {
				hasSign: false,
				integral: 0,
				signDays: 0
			}
		},
		onLoad(options) {
			let that = this;
			console.log(options)
			that.integral = options.integral;
			that.getSign();
		},
		methods:{
			// 获取签到信息
			getSign(){
				let that = this;
				uni.showLoading();
				uni.request({
					url: app.default.globalData.baseUrl + '/api/User/GetSign',
					method: 'post',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					dataType: "json",
					success(res) {
						console.log(res);
						if(res.data.code == 200){
							that.hasSign = res.data.data.daySign;
							that.signDays = res.data.data.signNumber;
						}
						uni.hideLoading()
						setTimeout(function(){
							
						},1500)
					},
					fail(res){
						console.log(res)
					}
				})
			},
			// 签到
			signin(){
				let that = this;
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/Sign",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					data:{
						openId: app.default.globalData.openId
					},
					dataType: "json",
					success(res) {
						console.log(res);
						uni.showToast({
							title: res.data.message,
							icon: 'none',
							mask: true,
							duration: 1500
						})
						setTimeout(function(){
							
						},1500)
					},
					fail(res){
						console.log(res)
					}
				})
			}
		}
	}
</script>

<style>
	.container {
		background-color: transparent;
	}
	.bg1 {
		position: fixed;
		left: 0;
		top: 0;
		z-index: -2;
		width: 100%;
		height: 100%;
	}
	.info {
	}
	.info .desc {
		padding: 60upx 0 ;
		font-size: 56upx;
		color: #E9CD9B;
		text-align: center;
	}
	.info .content {
		position: relative;
		width: 750upx;
		height: 1000upx;
		margin: 0 auto;
	}
	.info .bg2 {
		position: absolute;
		left: 0;
		top: 0;
		z-index: -1;
		width: 100%;;
		height: 100%;
	}
	.info .integral {
		height: 200upx;
		text-align: center;
		color: #fff;
		display: flex;
		flex-direction: column;
		justify-content: center;
	}
	.info .integral .score {
		font-size: 40upx;
		line-height: 60upx;
	}
	.info .integral .text {
		font-size: 24upx;
		line-height: 40upx;
	}
	.info .signDays {
		margin-top: 280upx;
		font-size: 26upx;
		text-align: center;
	}
	.info .signDays text {
		font-size: 50upx;
	}
	
	.signStatus {
		margin-top: 120upx;
		padding: 0 70upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		text-align: center;
	}
	.signStatus image {
		width: 46upx;
		height: 46upx;
	}
	.signStatus-item {
		flex: 1;
		font-size: 22upx;
	}
	.handle {
		margin-top: 40upx;
	}
	.handle button {
		width: 460upx;
		height: 88upx;
		border-radius: 88upx;
		color: #ccc;
	}
	.handle button.hasSign {		
		background: linear-gradient(#D8BB88, #E9CD9B);
		color: #fff;
	}
	.handle button::after{
		border: none;
	}
</style>
