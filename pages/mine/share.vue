<template>
	<view class="container">
		<view class="posters">
			<image src="/static/imgs/399product.jpg" mode="aspectFill"></image>
		</view>
		<view class="handle">
			<button class="download">下载海报</button>
			<button class="share" open-type="share">分享朋友</button>
		</view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		onLoad(options){
			let that = this;
			if (options.openId) {
				uni.showModal({
					title: 'openId',
					content: options.openId
				})
			}
		},
		onShareAppMessage(){
			if (res.from === 'button') {// 来自页面内分享按钮
			  console.log(res.target)
			}
			return {
			  title: '我的分享',
			  path: `/pages/mine/share?openId=${app.default.globalData.openId}`,
			  success: function(res) {
				// 转发成功
				console.log(res)
			  },
			  fail: function(res) {
				// 转发失败
				console.log(res)
			  }			  
			}			
		}
	}
</script>

<style>
	.container {
		padding-bottom: 98upx;
	}
	.posters {
		width: 100%;
		height: 100%;
	}
	.posters image {
		width: 100%;
	}
	.handle {
		position: fixed;
		left: 0;
		bottom: 0;
		z-index: 1;
		width: 100%;
		height: 98upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.handle button {
		flex: 1;
		height: 100%;
		font-size: 34upx;
		color: #fff;
		border-radius: 0;
	}
	.handle .download {
		background-color: #E9CD9B;
	}
	.handle .share {
		background-color: #313131;
	}	
</style>
