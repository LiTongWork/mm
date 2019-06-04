<template>
	<view class="container">
		<view class="list">
			<view class="list-item" v-for="(item,index) in list" :key='index'>
				<view class="icon" @tap="selAddress" :data-id='item.id'>
					<image v-if="item.status" src="/static/imgs/selected.png"></image>
					<image v-else src="/static/imgs/select.png"></image>
				</view>
				<view class="content" @tap="selAddress" :data-id='item.id'>
					<view>
						<text class="name">{{item.name}}</text>
						<text class="mobile">{{item.mobile}}</text>
					</view>
					<view class="address">{{item.address}}</view>
				</view>
				<view class="edit" @tap="toEdit" :data-id='item.id'><image src="/static/imgs/edit.png"></image></view>
			</view>
		</view>
		<view class="newAddress" @tap="toNewAddress">新增收货地址</view>
	</view>
</template>

<script>
	export default {
		data(){
			return {
				list: [
					{
						id: 1,
						status: true,
						name: '猪猪',
						mobile: 18312341234,
						address: '安徽省合肥市蜀山区荷叶地街道1'
					},
					{
						id: 2,
						status: false,
						name: '猪猪',
						mobile: 18312341234,
						address: '安徽省合肥市蜀山区荷叶地街道2'
					},
					{
						id: 3,
						status: false,
						name: '猪猪',
						mobile: 18312341234,
						address: '安徽省合肥市蜀山区荷叶地街道3'
					}
				]
			}
		},
		methods:{
			selAddress(e){
				let that = this;
				let addressId = e.currentTarget.dataset.id;
				console.log(addressId)
			},
			// 跳转
			toEdit(e){
				// 编辑地址
				let that = this;
				let addressId = e.currentTarget.dataset.id;
				uni.navigateTo({
					url: `/pages/mine/editAddress?addressId=${addressId}`
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
