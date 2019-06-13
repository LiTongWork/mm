<template>
	<view class="page-body">
		<!-- <view class="wei-list">
			<image class="wei-pic" src="" mode="scaleToFill"></image>
		</view> -->
		<!-- 阴影部分 -->
		<view class="wei-new">
			<text>申请获取以下权限</text>
			<text style="font-size: 26upx; color: #5F5D5D;">获得你的公开信息（昵称，头像等）</text>
		</view>
		<view class="wei-btn">
			<!-- <button class="wei-tag-btn" open-type="getUserInfo" @getuserinfo="mpGetUserInfo">授权登陆</button> -->
			<!-- #ifdef APP-PLUS || MP-ALIPAY || MP-TOUTIAO -->
			<!-- <text class="mine-btn">立即登录</text> -->
			<button class="wei-tag-btn" type="primary" @click="getUserInfo">授权登录</button>
			<!-- #endif -->
			<!-- #ifdef MP-WEIXIN || MP-BAIDU -->
			<button class="wei-tag-btn" type="primary" open-type="getUserInfo" @getuserinfo="mpGetUserInfo">授权登录</button>
			<!-- #endif -->
		</view>
	</view>
</template>
<script>
	var app = require("../../App.vue");
	export default {
		data() {
			return {
				hasUserInfo: false,
				userInfo: {} //保存用户信息
			}
		},
		onLoad(options) {
			console.log(options)
			let fromUrl= "" 
			// 第三种 普通二维码
			if(options.q){
			  let q = decodeURIComponent(options.q);
				console.log(q)
			}
			// 第二种  小程序码
			if(options.scene) {
				let scene=decodeURIComponent(options.scene);
				//&是我们定义的参数链接方式
				fromUrl=options.scene.split("&")[0];
			}else{
				fromUrl=options.openId
				// uni.showToast({
				// 	title:"=======",
				// 	duration:2000
				// })
			}
			let that = this;
			wx.login({
				provider: 'weixin',
				success: function(res) {
					// console.log("code", res.code)
					if (res.code) {
						uni.request({
							url: app.default.globalData.baseUrl + "/api/login/UserLogin",
							method: "POST",
							header: {
								'content-type': 'application/x-www-form-urlencoded'
							},
							dataType: "json",
							data: {
								wechatCode: res.code,
								metadata:fromUrl
							},
							success(e) {
								// console.log("e:" + JSON.stringify(e.data));
								app.default.globalData.openId = e.data.data.openId;
								app.default.globalData.token = e.data.data.token;
								wx.getSetting({
									success(res) {
										// console.log(JSON.stringify(res.authSetting))
										if(res.authSetting["scope.userInfo"]){
											that.mpGetUserInfo()
										}else{
											return
										}
									}
								})
							}
						})
					}
				}
			})
			// if(!options.openId) {
			// 	
			// }else {
			// 	uni.showToast({
			// 		title:options.openId,
			// 		duration:2000
			// 	})
			// }
			
			console.log(app.default.globalData.openid)
		},
		methods: {
			// 获取用户信息 API 在小程序可直接使用，在 5+App 里面需要先登录才能调用
			mpGetUserInfo(result) {
				var that = this;
				console.log('mpGetUserInfo', result);
				uni.getUserInfo({
					provider: 'weixin',
					success: (result) => {
						that.userInfo = result.userInfo;
						// console.log('that.userInfo' + JSON.stringify(that.userInfo)); //微信授权获得的信息
						// console.log('openId' + JSON.stringify(app.default.globalData.openId));
						// console.log('token' + JSON.stringify(app.default.globalData.token));
						uni.request({
							url: app.default.globalData.baseUrl + "/api/User/UpdateUser",
							method: "POST",
							header: {
								'content-type': 'application/x-www-form-urlencoded',
								"auth": app.default.globalData.token
							},
							dataType: "json",
							data: {
								HeadImg: JSON.stringify(that.userInfo.avatarUrl),
								NickName: JSON.stringify(that.userInfo.nickName)
							},
							success(res) {
								console.log(res.data.code)
								uni.switchTab({
									url: "../index/index"
								})
							}
						})
					}
				})
			},
			getQueryString(url,name){
				var reg = new RegExp('(^|&|/?)' + name + '=([^&|/?]*)(&|/?|$)', 'i')
				var r = url.substr(1).match(reg)
				if (r != null) {
				return r[2]
				}
				return null;				
			}

		}
	}
</script>
<style>
	@import "login.css";

	view {
		display: flex;
	}

	.page-body {
		width: 100%;
		height: 100vh;
		flex-direction: column;
		justify-content: center;
	}

	.body {
		display: flex;
		flex-direction: column;
		margin-bottom: 130px;
	}
</style>
