<template>
	<view class="container">
		<view class="head">
			<image class="bg" src="/static/imgs/mine-bg.png"></image>
			<view class="content">
				<view class="icon"><image src="/static/imgs/gold.png"></image></view>
				<view class="money">￥{{money}}</view>
			</view>
		</view>
		<view class="handle"><button>佣金提现</button></view>
	</view>
</template>

<script>
	export default {
		data(){
			return {
				money: '200.00'
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
	.handle {
		margin: 180upx 0;
	}
	.handle button {
		margin: 0 46upx;
		color: #fff;
		background-color: #313131;
	}
</style>
