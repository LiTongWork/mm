<template>
	<view class="page-body">
		<!-- 收货地址 -->
		<view class="addr">
			<view class="add-item" @click="selectAddress" v-if="province==''">
				<view class="add-l">
					<image class="add-icon" src="../../static/imgs/addricon.png" mode="aspectFit"></image>
					<text>新增地址</text>
				</view>
				<uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon>
			</view>
			<view class="add-item" v-if="province!=''" @click="goAddress">
				<view class="add-name">
					<image class="add-pic" src="../../static/imgs/address.png" mode="aspectFit"></image>
					<view class="add-tag">
						<view class="add-text">
							<text style="font-size: 35rpx;">{{consigneeName}}</text><text style="color: #A2A2A2;margin-left: 20rpx;">{{consigneeMobile}}</text>
						</view>
						<text>{{province+city+area+address}}</text>
					</view>
				</view>
				<uni-icon type="arrowright" size="20" color="#a2a2a2"></uni-icon>
			</view>
		</view>
		<!-- 购买商品列表 -->
		<view class="goods-list" v-for="(value,index) in buylist" :key="index">
			<view class="goods-store">
				<image class="goods-icon" src="/static/imgs/store-icon.png" mode="aspectFit"></image>
				<text>平台自营</text>
			</view>
			<view class="goods-tag">
				<image class="goods-img" :src="imgUrl+value.goodsImg" mode="aspectFit"></image>
				<view class="goods-item">
					<text>{{value.goodsName}}</text>
					<text style="color: #999999;">运费:{{value.freight}}</text>
					<view class="goods-price" v-if="value.payMethod==1">
						<text style="color: red;font-size: 30rpx;">￥{{value.currentPice}}</text> <text>数量: {{value.number}}</text>
					</view>
					<view class="goods-price" v-if="value.payMethod==2">
						<text style="color: red;font-size: 30rpx;">{{value.currentPice}}积分</text> <text>数量: {{value.number}}</text>
					</view>
				</view>
			</view>
			<view class="goods-heji" v-if="value.payMethod==1">
				<text>合计：</text><text style="color: red;font-size: 30rpx;">￥{{value.currentPice*value.number}}</text>
			</view>
			<view class="goods-heji" v-if="value.payMethod==2">
				<text>合计：</text><text style="color: red;font-size: 30rpx;">{{value.currentPice*value.number}}积分</text>
			</view>
		</view>
		<!-- 提交订单按钮 -->
		<view class="order-tag" v-if="payMethod==1">
			<text>合计:</text><text style="color: red;">￥{{allPrice}}</text>
			<text class="order-btn" @click="trueBtn">提交订单</text>
		</view>
		<view class="order-tag" v-if="payMethod==2">
			<text>合计:</text><text style="color: red;">{{allPrice}}积分</text>
			<text class="order-btn" @click="trueBtn">提交订单</text>
		</view>
	</view>
</template>

<script>
	var app = require("../../App.vue")
	import uniIcon from "@/components/uni-icon/uni-icon.vue"
	export default {
		components: {
			uniIcon
		},
		data() {
			return {
				imgUrl: app.default.globalData.imgUrl, //图片拼接位置
				buylist: [], //订单列表
				consigneeName:"",
				consigneeMobile:"",
				province:"",
				city:"",
				area:"",
				address:"",
				allPrice: "",
				goodsChecked: [],
				payMethod:""//支付方式1是元 ，2时积分
			};
		},
		onLoad(e) {
			console.log(e.goodsChecked);
			// console.log(e.sumPrice);
			this.allPrice = e.sumPrice
			this.goodsChecked = JSON.parse(e.goodsChecked)
			this.orderList(this.goodsChecked);
			this.add();
		},
		onShow() {
			// this.orderList(goodsChecked);
			this.add();
		},
		methods: {
			add: function() {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/json',
						"auth": app.default.globalData.token
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/User/UserAddressList",
					data: {
						"Status": -1
					},
					success(res) {
						console.log(res.data)
						that.consigneeName = res.data.data[0].consigneeName;
						that.consigneeMobile = res.data.data[0].consigneeMobile;
						that.province = res.data.data[0].province;
						that.city = res.data.data[0].city;
						that.area = res.data.data[0].area;
						that.address = res.data.data[0].address;
					}
				})
			},
			orderList: function(goodsChecked) {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/json',
						"auth": app.default.globalData.token
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/User/PrepaidOrder",
					data: {
						"goodsChecked": goodsChecked
					},
					success(res) {
						that.buylist = res.data.data
						for(var i = 0;i<that.buylist.length;i++){
							that.payMethod=that.buylist[i].payMethod
							console.log(that.payMethod)
						}
					}
				})
			},
			trueBtn() {
				let that = this;
				let  orderUrl
				if(that.province=="") {
					wx.showToast({
					  title: '请填写地址',
					  icon: "none",
					  mask: true,
					  duration: 1500
					})
					return
				}
				if(that.payMethod==1) {
					orderUrl="/api/User/SaveOrder"
				}
				if(that.payMethod==2){
					orderUrl= "/api/User/IntegralSaveOrder"
				}
				uni.request({
					header: {
						'content-type': 'application/json',
						"auth": app.default.globalData.token
					},
					url:app.default.globalData.baseUrl+orderUrl,
					method: "POST",
					dataType: 'json',
					data: {
						userAddress: that.province+that.city+that.area+that.address,
						isShopping: true,
						goodsChecked: that.goodsChecked
					},
					success(res) {
						console.log(res.data);
						if(that.payMethod ==1) {
							wx.requestPayment({
							  timeStamp: res.data.data.data.timeStamp,
							  nonceStr: res.data.data.data.nonceStr,
							  package: res.data.data.data.prepayId,
							  signType: 'MD5',
							  paySign: res.data.data.data.sign,
							  success(res) {
							    wx.showToast({
							      title: '商品购买成功',
							      icon: 'success',
							      mask: true,
							      duration: 2000
							    })
							    wx.switchTab({
							      url: '/pages/mine/index'
							    })
							  },
							  fail(res) { 
							    wx.showToast({
							      title: '商品购买失败',
							      icon: 'none',
							      mask: true,
							      duration: 2000
							    })
							    wx.switchTab({
							      url: '/pages/mine/index'
							    })
							  }
							})
						}else{
							 wx.showToast({
							  title: '商品购买成功',
							  icon: 'none',
							  mask: true,
							  duration: 2000
							})
							setTimeout(function() {
								wx.switchTab({
								  url: '/pages/mine/index'
								})
							}, 2000);
							
						}
					}
				})
			},
			//选择收货地址
			selectAddress() {
				uni.navigateTo({
					url: '../mine/newAddress'
				})
			},
			goAddress() {
				uni.navigateTo({
					url: '../mine/address'
				})
			}
		}
	}
</script>

<style lang="scss">
	view {
		display: flex;
	}

	.page-body {
		display: flex;
		flex-direction: column;
		background-color: #F2F2F2;
		height: 100vh;
	}

	.addr {
		height: 200rpx;
		background-color: #FFFFFF;
		display: flex;
	}
	.add-name {
		font-size: 30rpx;
		line-height: 180%;
	}
	.add-pic {
		height: 60rpx;
		width: 100rpx;
		margin-right: 10rpx;
		align-self: center;
	}
	.add-icon {
		display: inline-flex;
		align-self: center;
		height: 60rpx;
		width: 60rpx;
		margin-right: 10rpx;
	}

	.add-l {
		display: inline-flex;
		flex: 3;
		align-items: center;
	}

	.add-item {
		display: flex;
		flex: 1;
		justify-content: space-between;
		padding: 40rpx 20rpx;
		align-items: center;
	}

	.add-tag {
		flex-direction: column;
	}

	// 商品订单列表
	.goods-list {
		width: 85%;
		flex-direction: column;
		background-color: #FFFFFF;
		font-size: 26rpx;
		margin: 25rpx auto;
		padding: 20rpx;
		border-radius: 5rpx;
	}

	.goods-icon {
		height: 40rpx;
		width: 40rpx;
		margin-right: 20rpx;
	}

	.goods-store {
		padding: 20rpx 0rpx;
		font-size: 30rpx;
		align-items: center;
	}

	.goods-tag {
		border-bottom: 1rpx solid #A2A2A2;
		padding: 30rpx 10rpx;
	}

	.goods-img {
		height: 200rpx;
		width: 200rpx;
	}

	.goods-item {
		flex-direction: column;
		padding-left: 20rpx;
		line-height: 200%;
		flex: 1;
	}

	.goods-price {
		justify-content: space-between;
	}

	.goods-heji {
		justify-content: flex-end;
		padding: 20rpx 10rpx;
		align-items: center;
	}

	// 提交订单按钮
	.order-tag {
		justify-content: flex-end;
		position: fixed;
		bottom: 0;
		width: 750rpx;
		align-items: center;
		font-size: 26rpx;
		padding: 5rpx 0rpx;
		background-color: #FFFFFF;
	}

	.order-btn {
		height: 100rpx;
		width: 250rpx;
		background-color: #313131;
		color: #FFFFFF;
		border-radius: 60rpx;
		margin-left: 15rpx;
		line-height: 100rpx;
		text-align: center;
		margin: 3rpx 0rpx;
		margin-right: 30rpx;
		margin-left: 15rpx;
	}
</style>
