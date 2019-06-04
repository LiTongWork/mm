<template>
	<view class="container">
		<view class="list">
			<view class="list-item" v-for="(item,index) in list" :key='index'>
				<view class="detail">
					<view class="content">{{item.content}}</view>
					<view class="createTime">{{item.createTime}}</view>
				</view>
				<view class="money">+{{item.money}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [
					{
						content: '天问三誓',
						createTime: '2019-06-03',
						money: '200.00'
					},
					{
						content: '抓风成石',
						createTime: '2019-06-03',
						money: '200.00'
					}					
				]
			}
		}
	}
</script>

<style>
	.list-item {
		box-sizing: border-box;
		padding: 0 46upx;
		height: 120upx;
		background-color: #fff;
		border-bottom: 1px solid #e9e9e9;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.list-item .detail {
		
	}
	.list-item .detail .content {
		font-size: 28upx;
		line-height: 50upx;
	}
	.list-item .detail .createTime {
		font-size: 20upx;
		color: #a2a2a2;
	}
	.list-item .money {
		font-size: 34upx;
		color: #E9CD9B;
	}
</style>
