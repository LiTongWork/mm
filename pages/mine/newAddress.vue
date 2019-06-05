<template>
	<view class="container">
		<view class="list">
			<view class="list-item">
				<view class="title">姓名</view>
				<view class="input">
					<input type="text" v-model="consigneeName" placeholder="请输入姓名" maxlength="20"/>
				</view>
			</view>
			<view class="list-item">
				<view class="title">手机号码</view>
				<view class="input">
					<input type="number" v-model="consigneeMobile" placeholder="请输入手机号码" maxlength="11" />
				</view>
			</view>
			<view class="list-item">
				<view class="title">选择地区</view>
				<view class="input" @tap="chooseCity">
					<input type="text" readonly v-model="region.label" placeholder="请选择地址" />
				</view>
			</view>
			<view class="list-item">
				<view class="title">详细地址</view>
				<view class="input address">
					<input v-model="address" placeholder="请输入详细地址" maxlength="50" />
				</view>
			</view>
		</view>
		<view class="handle">
			<button class="save" @tap="save">保存</button>
			<!-- <button class="delete">删除收货地址</button> -->
		</view>
		<mpvue-city-picker :themeColor="themeColor" ref="mpvueCityPicker" :pickerValueDefault="cityPickerValue" @onCancel="onCancel" @onConfirm="onConfirm"></mpvue-city-picker>
		
	</view>
</template>

<script>
	const app = require('../../App.vue');
	import mpvueCityPicker from '@/components/mpvue-citypicker/mpvueCityPicker.vue'
	export default {
		components: {
			mpvueCityPicker
		},
		data(){
			return {
				// addressId: '',
				consigneeName: '',
				consigneeMobile: '',
				cityPickerValue: [0, 0, 1],
				themeColor: '#007AFF',
				region:{label:"",value:[],cityCode:""},
				address: ''
			}
		},
		onLoad(options) {
			let that = this;
		},
		methods: {
			// 保存地址
			save(){
				let that = this;
				if (that.consigneeName == '') {
					uni.showToast({
						title: '姓名不能为空',
						icon: 'none',
						mask: true,
						duration: 1500
					})
				} else if (that.consigneeMobile == '') {
					uni.showToast({
						title: '手机号码不能为空',
						icon: 'none',
						mask: true,
						duration: 1500
					})
				} else if (!that.phoneReg(that.consigneeMobile)) {
					uni.showToast({
						title: '手机号码有误',
						icon: 'none',
						mask: true,
						duration: 1500
					})
				} else if (that.region.label == '') {
					uni.showToast({
						title: '请选择地址',
						icon: 'none',
						mask: true,
						duration: 1500
					})					
				} else if (that.address == '') {
					uni.showToast({
						title: '请填写详细地址',
						icon: 'none',
						mask: true,
						duration: 1500
					})					
				} else {
					let dd = that.region.label.split('-');
					let params = {
						consigneeName: that.consigneeName,
						consigneeMobile: that.consigneeMobile,
						province: dd[0],
						city: dd[1],
						area: dd[2],
						address: that.address
					}
					console.log(params);
					uni.request({
						url: app.default.globalData.baseUrl + "/api/User/UserAddressEdit",
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
								setTimeout(() => {
									uni.navigateBack()
								},1500)
							}
						},
						fail(res){
							console.log(res)
						}	
					})
				}
			},
			onCancel(e) {
				console.log(e);
			},
			chooseCity() {
				console.log('dd')
				this.$refs.mpvueCityPicker.show()
			},
			onConfirm(e) {
				this.region = e;
				console.log(e)
				this.cityPickerValue = e.value;
			},
			phoneReg(phone) {
			  return /^1[34578]\d{9}$/.test(phone)
			}
		}
	}
</script>

<style>
	.list-item {
		padding: 0 46upx;
		line-height: 100upx;
		background-color: #fff;
		border-bottom: 1px solid #e9e9e9;
		font-size: 30upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.list-item .title {
		width: 150upx;
	}
	.list-item .input {
		padding-left: 20upx;
		height: 100upx;
		flex: 1;
	}
	.list-item .input input {
		width: 100%;
		height: 100%;
	}
	/* 操作按钮 */
	.handle {
		padding: 46upx;
	}
	.handle button {
		margin-bottom: 30upx;
	}
	.handle .save {
		color: #fff;
		background-color: #313131;
	}
	.handle .delete {
		color: #313131;
		background-color: #fff;
	}
	
</style>
