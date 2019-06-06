<template>
	<view class="container">
		<view class="top">
			<view class="title">总资产(CNY)</view>
			<view class="money">
				<text class="totalMoney">{{totalMoney}}</text>
				<view class="handle" @tap="toYjtx">
					<text>提现</text>
					<uni-icon type="arrowright" size="20" color="#fff"></uni-icon>
				</view>
			</view>
			<view class="earnings">
				<view class="earnings-yesterday">昨日收益：<text>￥{{earningsYesterday}}</text></view>
				<view class="earnings-all">累计收益：<text>￥{{earningsAll}}</text></view>
			</view>
		</view>
		<!-- 明细 -->
		<view class="detail">
			<view class="title">明细</view>
			<view class="list">
				<view class="list-item" v-for="(item,index) in list" :key='index'>
					<view>
						<text class="label" v-if="item.FinanceLogModel == 1">佣金</text>
						<text class="label" v-if="item.FinanceLogModel == 2">提现</text>
						<text class="money" v-if="item.FinanceLogModel == 1">+{{item.money}}</text>
						<text class="money" v-if="item.FinanceLogModel == 2">-{{item.money}}</text>
					</view>
					<view>
						<text class="desc">{{item.Note}}</text>
						<text class="createTime">{{item.createTime}}</text>
					</view>
				</view>
				<view class="noData">暂无数据</view>
			</view>
		</view>
	</view>
</template>

<script>
	const app = require('../../App.vue')
	import uniIcon from "@/components/uni-icon/uni-icon.vue"
	export default {
		components: {uniIcon},
		data () {
			return {
				totalMoney: 0.00,
				earningsYesterday: 0.00,
				earningsAll: 0.00,
				list: [],
				page: 1,
				rows: 10
			}
		},
		onLoad() {
			let that = this;
			that.getTotalMoney()
			that.getInfo()
		},
		onPullDownRefresh() {
			let that = this;
			that.getTotalMoney();
			that.page = 1;
			that.list = [];
			that.getInfo()
		},
		onReachBottom() {
			let that = this;
			that.page++;
			that.getInfo();
		},
		methods: {
			// 获取总资金
			getTotalMoney(){
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
						// console.log(res)
						if(res.data.code == 200) {
							that.totalMoney = (Number(res.data.data.surplus)).toFixed(2);
						}
					},
					fail(res){
						console.log(res)
					}
				})	
			},
			// 获取列表
			getInfo() {
				let that = this;
				let params = {
					page: that.page,
					rows: that.rows
				}
				uni.showLoading();
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/CapitaldetailsPage",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: "json",
					success(res) {
						// console.log(res.data.data.list);
						uni.hideLoading();
						uni.stopPullDownRefresh();
						if(res.data.code == 200) {
							that.earningsYesterday = (Number(res.data.data.yesterday)).toFixed(2);
							that.earningsAll = (Number(res.data.data.cumulative)).toFixed(2);
							for (let i in res.data.data.list){
							  let createTime = res.data.data.list[i].createTime;
							  let arr = createTime.split('T');
							  let arr2 = arr[1].split(':');
							  createTime = arr[0]+ ' '+ arr2[0] + ':'+ arr2[1];
							  res.data.data.list[i].createTime = createTime;
							}
							that.list = that.list.concat(res.data.data.list)
						}
					},
					fail(res){
						console.log(res)
					}
				})				
			},
			// 跳转
			toYjtx(){
				// 佣金提现
				uni.navigateTo({
					url: '/pages/fenxiao/yjtx'
				})
			}
		}

	}
</script>

<style>
	.container {
		padding: 36upx 46upx;
	}
	.top {
		box-sizing: border-box;
		padding: 20upx 30upx 40upx;
		color: #fff;
		background: linear-gradient(to bottom right, #717171 , #313131); /* 标准的语法 */
		border-radius: 12upx;
		font-size: 26upx;
	}
	.top .title {
		line-height: 60upx;
	}
	.top .money, .top .earnings {
		display: flex;
		align-items: flex-end;
		justify-content: space-between;
	}
	.top .money {
		min-height: 120upx;
		line-height: 120upx;
	}
	.top .money .totalMoney {
		font-size: 40upx;
		font-weight: bold;
	}
	.top .money .handle {
		display: flex;
		align-items: center;
	}
	.top .earnings .earnings-all {
		color: #E9CD9B;
	}
	/* 明细列表 */
	.detail .title {
		line-height: 120upx;
		font-size: 30upx;
	}
	.detail .list-item {
		box-sizing: border-box;
		padding: 10upx 20upx;
		background-color: #fff;
		border-radius: 10upx;
		margin-bottom: 20upx;
		font-size: 26upx;
		line-height: 60upx;
	}
	.detail .list-item view {
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.detail .list-item view:first-child .money {
		color: #FE3F10;
	}
	.detail .list-item view:last-child {
		color: #a2a2a2;
	}
</style>
