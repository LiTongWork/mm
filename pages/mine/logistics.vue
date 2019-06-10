<template>
	<view class="container">
		<view class="list"> 
			<view class="list-item">
				<!-- <view class="pic"><image :src="item.pic"></image></view> -->
				<view class="desc">
					<view class="name">快递公司:<text>{{list.logisticsName ? list.logisticsName : ''}}</text></view>
					<view class="number">快递单号:<text>{{list.logisticsCode ? list.logisticsCode : ''}}</text></view>
					<!-- <view class="tel">联系电话:<text>{{list.tel}}</text></view> -->
				</view>
				<view class="copy" @tap="copyNumber" :data-number='list.logisticsCode'>复制单号</view>
			</view>
		</view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		data(){
			return {
				indentId: '',
				list: {}
			}
		},
		onLoad(options) {
			let that = this;
			if(options.indentId) {
				that.indentId = options.indentId;
				that.getList();
			}else {
				uni.showToast({
					title: 'indentId不能为空',
					icon: 'none',
					mask: true,
					duration: 1500
				})
			}
		},
		methods: {
			// 获取列表
			getList () {
				let that = this;
				uni.showLoading();
				let params = {
					indentId: that.indentId
				}
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/CheckLogistics",
					method: "POST",
					header: {
						'content-type': 'application/json',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: "json",
					success(res) {
						console.log(res);
						uni.hideLoading();
						if(res.data.code == 200) {
							that.list = res.data.data
						}
					},
					fail(res){
						console.log(res)
					}
				})					
			},
			// 复制单号
			copyNumber (e){
				let that = this;
				uni.setClipboardData({  
                    data: e.currentTarget.dataset.number,  
                    success: (r => {  
						wx.getClipboardData({
						  success: function (res) {
							wx.showToast({
							  title: '复制成功'
							})
						  }
						})							
                    })  
                })
			}
		}
	}
</script>

<style>
	.list {
		padding: 36upx 46upx;
		background-color: #fff;
		border-bottom: 1px solid #e9e9e9;
	}
	.list .list-item {
		font-size: 26upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.list-item .pic, .list-item .pic image {
		width: 150upx;
		height: 150upx;
	}
	.list-item .desc {
		flex: 1;
		padding: 0 20upx;
		line-height: 50upx;
	}
	.list-item .desc text {
		padding: 0 16upx;
	}
	.list-item .copy {
		padding: 10upx 20upx;
		border: 1px solid #e9e9e9;
		border-radius: 10upx;
	}
</style>
