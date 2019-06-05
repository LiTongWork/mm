<template>
	<view class="container">
		<view class="list">
			<view class="list-item" v-for="(item,index) in list" :key='index'>
				<view class="icon" @tap="selAddress" :data-id='item.id' :data-index='index'>
					<image v-if="item.status == 1" src="/static/imgs/selected.png"></image>
					<image v-else src="/static/imgs/select.png"></image>
				</view>
				<view class="content" @tap="selAddress" :data-id='item.id' :data-index='index'>
					<view>
						<text class="name">{{item.consigneeName}}</text>
						<text class="mobile">{{item.consigneeMobile}}</text>
					</view>
					<view class="address">{{item.province + item.city + item.area + item.address}}</view>
				</view>
				<view class="edit" @tap="toEdit" :data-item='item'><image src="/static/imgs/edit.png"></image></view>
			</view>
			<view class="noData" v-if="list.length == 0">暂无数据</view>
		</view>
		<view class="newAddress" @tap="toNewAddress">新增收货地址</view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		data(){
			return {
				list: []
			}
		},
		onShow(){
			let that = this;
			that.list = [];
			that.getList()
		},
		onPullDownRefresh(){
			let that = this;
			that.list = [];
			that.getList();
		},
		methods:{
			// 获取地址列表
			getList(){
				let that = this;
				uni.showLoading();
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/UserAddressList",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					data: {
						status: -1
					},
					dataType: "json",
					success(res) {
						console.log(res);
						uni.hideLoading();
						uni.stopPullDownRefresh();
						if(res.data.code == 200) {
							that.list = that.list.concat(res.data.data);
						}
					},
					fail(res){
						console.log(res)
					}
				})				
			},
			//  选择地址,设为默认地址
			selAddress(e){
				let that = this;
				let addressId = e.currentTarget.dataset.id;
				let i = e.currentTarget.dataset.index;
				console.log(addressId)
				let params = {
					id: addressId
				}
				console.log(params)
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/UpdaUserAddress",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: "json",
					success(res) {
						console.log(res);
						uni.showToast({
							title: res.data.message,
							icon: 'none',
							mask: true,
							duration: 1500
						})
						if(res.data.code == 200) {
							that.list.forEach((item,index) => {
								console.log(item,index)
								if (index == i) {
									item.status = 1;
								} else {
									item.status = 0;
								}
							})
							setTimeout(() => {
								uni.navigateBack()
							},1500)
						}
					},
					fail(res){
						console.log(res)
					}	
				})					
			},
			// 跳转
			toEdit(e){
				// 编辑地址
				let that = this;
				let item = e.currentTarget.dataset.item;
				uni.navigateTo({
					url: `/pages/mine/editAddress?item=${JSON.stringify(item)}`
				})
			},
			toNewAddress(e){
				// 新增地址
				uni.navigateTo({
					url:'/pages/mine/newAddress'
				})
			}
		}
	}
</script>

<style>
	.container {
		padding-bottom: 100upx;
	}
	.list-item {
		padding: 30upx 46upx;
		background-color: #fff;
		border-bottom: 1px solid #e9e9e9;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.list-item .icon, .list-item .icon image {
		width: 40upx;
		height: 40upx;
	}
	.list-item .content {
		flex: 1;
		padding: 0 20upx;
	}
	.list-item .content .name {
		font-size: 32upx;
		color: #3D3C3C;
		line-height: 50upx;
	}
	.list-item .content .mobile {
		font-size: 24upx;
		color: #ccc;
		line-height: 50upx;
		margin-left: 20upx;
	}
	.list-item .content .address {
		font-size: 24upx;
		line-height: 36upx;
	}
	.list-item .edit, .list-item .edit image {
		width: 30upx;
		height: 30upx;
	}
	/* 新增按钮 */
	.newAddress {
		position: fixed;
		left: 0;
		bottom: 0;
		z-index: 1;
		width: 100%;
		height: 100upx;
		line-height: 100upx;
		font-size: 32upx;
		color: #fff;
		background-color: #313131;
		text-align: center;
	}
</style>
