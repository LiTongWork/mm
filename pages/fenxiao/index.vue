<template>
	<view class="container">
		<view class="header-info">
			<image class="bg" src="/static/imgs/fenxiao-bg.png"></image>	
			<view class="avatar"><image :src='userInfo.headImg ? userInfo.headImg : "/static/imgs/default.png" ' ></image></view>
			<view class="desc">
				<view class="name">{{userInfo.nickName}}</view>
				<view class="promote">推介人:{{userInfo.driveNickName ? userInfo.driveNickName : '无'}}</view>
				<view class="level"><text>V{{userInfo.level}}</text></view>
			</view>
		</view>
		<view class="commission">
			<view class="detail">
				<view class="money">{{userInfo.surplus}}</view>
				<view class="commission-desc">可提现赏金（元）</view>
				<view class="tip">{{tip}}</view>
			</view>
			<view class="handle"><button @tap="toYjtx">赏金提现</button></view>
		</view>
		<view class="list">
			<view class="list-item Yjtx" @tap="toYjtx">
				<view class="icon"><image src="/static/imgs/icon-purse.png"></image></view>
				<view class="content">
					<view class="title">累计赏金</view>
					<view><text>{{userInfo.surplus}}</text>元</view>
				</view>
			</view>
			<view class="list-item yjmx" @tap="toYjmx">
				<view class="icon"><image src="/static/imgs/icon-commission-detail.png"></image></view>
				<view class="content">
					<view class="title">赏金明细</view>
					<view><text>{{userInfo.userStatistics.commission}}</text>笔</view>
				</view>
			</view>		
			<view class="list-item txmx" @tap="toTxmx">
				<view class="icon"><image src="/static/imgs/icon-tixian-detail.png"></image></view>
				<view class="content">
					<view class="title">提现明细</view>
					<view><text>{{userInfo.userStatistics.deposit}}</text>笔</view>
				</view>
			</view>		
			<view class="list-item team" @tap="toTeam">
				<view class="icon"><image src="/static/imgs/icon-team.png"></image></view>
				<view class="content">
					<view class="title">我的粉丝</view>
					<view><text>{{userInfo.userStatistics.fans}}</text>人</view>
				</view>
			</view>					
		</view>
	</view>
</template>

<script>
	const app = require("../../App.vue");
	export default {
		data(){
			return {
				userInfo: {},
				tip: '*提取金额以100整数提取，每提取一次收取3%平台费'				
			}
		},
		onShow(){
			let that = this;
			that.getUserInfo();
		},
		methods:{
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
							res.data.data.surplus = res.data.data.surplus.toFixed(2);
							that.userInfo = res.data.data;
						}
					},
					fail(res){
						console.log(res)
					}
				})
			},
			// 跳转
			toYjtx(e){
				// 分销佣金
				uni.navigateTo({
					url: '/pages/fenxiao/yjtx'
				})
			},
			toYjmx(e){
				// 佣金明细
				uni.navigateTo({
					url: '/pages/fenxiao/yjmx'
				})
			},
			toTxmx(e){
				// 提现明细
				uni.navigateTo({
					url: '/pages/fenxiao/txmx'
				})
			},
			toTeam(e){
				// 我的粉丝
				uni.navigateTo({
					url: '/pages/fenxiao/team'
				})
			}			
		}
	}
</script>

<style>
	.header-info {
		position: relative;
		display: flex;
		padding: 26upx 46upx;
		background-color: transparent;
	}
	.header-info .bg {
		position: absolute;
		left: 0;
		top: 0;
		z-index: 0;
		width: 100%;
		height: 100%;
	}
	.header-info .avatar, .header-info .avatar image{
		width: 130upx;
		height: 130upx;
		z-index: 1;
		border-radius: 50%;
	}
	.header-info .desc {
		padding: 0 20upx;
		flex: 1;
		color: #fff;
		z-index: 1;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}
	.desc .name {
		font-size: 28upx;
	}
	.desc .promote {
		font-size: 24upx;
	}
	.desc .level text{
		font-size: 24upx;
		padding: 0 20upx;
		background-color: #E9CD9B;
		border-radius: 32upx;
	}
	/* 佣金 */
	.commission {
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 30upx 46upx;
		margin-top: 36upx;
		background-color: #fff;
	}
	.commission .detail {
		flex: 1;
		padding-right: 60upx;
	}
	.commission .detail .money {
		color: #E9CD9B;
		font-size: 38upx;
	}
	.commission .detail .commission-desc {
		font-size: 28upx;
		margin: 6upx 0;
	}
	.commission .detail .tip {
		color: #A2A2A2;
		font-size: 24upx;
	}	
	.commission .handle button {
		height: 52upx;
		line-height: 52upx;
		padding: 0 30upx;
		font-size: 26upx;
		color: #fff;
		background-color: #313131;
		border-radius: 52upx;
	}
	.commission .handle button::after {
		border: none;
	}
	/* 列表按钮 */
	.list {
		padding: 36upx 46upx;
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
	}
	.list-item {
		box-sizing: border-box;
		width: 326upx;
		margin-bottom: 6upx;
		background-color: #fff;
		display: flex;
		align-items: center;
		padding: 40upx 20upx;
		font-size: 28upx;
	}
	.list-item .icon, .list-item .icon image {
		width: 90upx;
		height: 90upx;
	}
	.list-item .content {
		padding-left: 20upx;
		line-height: 44upx;
	}
	.list-item .content text {
		color: #E9CD9B;
	}
</style>
