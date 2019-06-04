<template>
	<view class="container">
		<view class="list">
			<view class="list-item">
				<view class="title">姓名</view>
				<view class="input">
					<input type="text" v-model="name" placeholder="请输入姓名" />
				</view>
			</view>
			<view class="list-item">
				<view class="title">手机号码</view>
				<view class="input">
					<input type="number" v-model="mobile" placeholder="请输入手机号码" />
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
				<view class="input detailAddress">
					<textarea auto-height="true" v-model="detailAddress" placeholder="请输入详细地址" />
				</view>
			</view>
		</view>
		<view class="handle">
			<button class="save" @tap="save">保存并使用</button>
			<!-- <button class="delete">删除收货地址</button> -->
		</view>
		<mpvue-city-picker :themeColor="themeColor" ref="mpvueCityPicker" :pickerValueDefault="cityPickerValue" @onCancel="onCancel" @onConfirm="onConfirm"></mpvue-city-picker>
		
	</view>
</template>

<script>
	import mpvueCityPicker from '@/components/mpvue-citypicker/mpvueCityPicker.vue'
	export default {
		components: {
			mpvueCityPicker
		},
		data(){
			return {
				addressId: '',
				name: '',
				mobile: '',
				cityPickerValue: [0, 0, 1],
				themeColor: '#007AFF',
				region:{label:"",value:[],cityCode:""},
				detailAddress: ''
			}
		},
		onLoad(options) {
			let that = this;
			that.addressId = options.addressId;
			console.log(that.addressId)
		},
		methods: {
			// 保存地址
			save(){
				let that = this;
				console.log(that.name)
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
				this.cityPickerValue = e.value;
			},
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
	.list-item .input.detailAddress {
		min-height: 100upx;
		display: flex;
		align-items: center;
	}
	.list-item .input textarea {
		flex: 1;
		width: 100%;
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
