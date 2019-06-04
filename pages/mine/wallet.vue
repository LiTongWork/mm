<template>
	<view class="container">
		<view class="top">
			<view class="title">总资产(CNY)</view>
			<view class="money">
				<text class="totalMoney">{{totalMoney}}</text>
				<view class="handle">
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
				<view class="list-item" v-for="(item,index) in lsit" :key='index'>
					<view>
						<text class="label">{{item.label}}</text>
						<text class="money">+{{item.money}}</text>
					</view>
					<view>
						<text class="desc">{{item.desc}}</text>
						<text class="createTime">{{item.createTime}}</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniIcon from "@/components/uni-icon/uni-icon.vue"
	export default {
		data () {
			return {
				totalMoney: '188.00',
				earningsYesterday: '666.00',
				earningsAll: '9999.00',
				lsit: [
					{
						label: '秒大神',
						desc: '秒大神收益到账',
						money: '188.00',
						createTime: '03-01 22:00'
					},
					{
						label: '提现',
						desc: '提现到银行卡',
						money: '188.00',
						createTime: '03-01 22:00'
					}
				],
				page: 1,
				rows: 10
			}
		},
		components: {uniIcon},
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
