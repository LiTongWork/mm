<template>
	<view class="container">
		<view class="posters">
			<image :src="imgUrl + posters" mode="aspectFill"></image>
		</view>
		<view class="" style="align-self: center;">
			<image style="width: 250px;height: 250px;" :src="imgUrl+imgs" mode=""></image>
		</view>
		<view class="handle">
			<!-- <button class="download">下载海报</button> -->
			<button class="share" open-type="share">分享朋友</button>
		</view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		data () {
			return {
				imgUrl: app.default.globalData.imgUrl,
				posters: '',
				imgs:""
			}
		},
		onLoad(options){
			let that = this;
			that.getPosters();
			that.getMa()
		},
		onShareAppMessage(){
			// if (res.from === 'button') {// 来自页面内分享按钮
			//   console.log(res)
			// }
			return {
			  title: '我的分享',
			  path: `/pages/login/login?openId=${app.default.globalData.openId}`,
			  success: function(res) {
				// 转发成功
				console.log(res)
			  },
			  fail: function(res) {
				// 转发失败
				console.log(res)
			  }			  
			}			
		},
		onPullDownRefresh() {
			this.getPosters();
			this.getMa();
			uni.stopPullDownRefresh();
		},
		methods: {
			// 获取海报
			getPosters(){
				let that = this;
				uni.request({
					url: app.default.globalData.baseUrl + '/api/Home/AdvList',
					method: 'post',
					header: {
						'content-type': 'application/json',
						'auth': app.default.globalData.token
					},
					success(res) {
						console.log(res);
						if (res.data.code == 200) {
							that.posters = res.data.data[0].advUrl
						}
					},
					fail(res){
						console.log(res)
					}					
				})
			},
			getMa() {
				let that = this;
				uni.request({
					url: app.default.globalData.baseUrl + '/api/User/QrCode',
					method: 'post',
					header: {
						'content-type': 'application/json',
						'auth': app.default.globalData.token
					},
					success(res) {
						console.log(res);
						if (res.data.code == 200) {
							that.imgs = res.data.data
						}
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
		padding-bottom: 98upx;
		display: flex;
		flex-direction: column;
		background-color: #FFFFFF;
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
