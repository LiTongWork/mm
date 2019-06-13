<template>
	<view class="container">
		<view class="list">
			<view class="list-item" v-for="(item,index) in list" :key='index'>
				<view class="detail">
					<view class="content">{{item.content}}</view>
					<view class="createTime">{{item.createTime}}</view>
				</view>
				<view class="money">+{{item.money}}</view>
			</view>
		</view>
		<view class="noData" v-if="list.length == 0">暂无数据</view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		data() {
			return {
				list: [],
				page: 1,
				rows: 12
			}
		},
		onLoad() {
			let that = this;
			that.getList();
		},
		onPullDownRefresh() {
			let that = this;
			that.page = 1;
			that.list = [];
			that.getList();
		},
		onReachBottom() {
			let that = this;
			that.page++;
			that.getList();
		},
		methods: {
			// 获取列表
			getList(){
				let that = this;
				uni.showLoading();
				let params = {
					page: that.page,
					rows: that.rows
				}
				uni.request({
					url: app.default.globalData.baseUrl + '/api/Distribution/CommissionPage',
					method: 'post',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: 'json',
					success(res) {
						uni.hideLoading();
						uni.stopPullDownRefresh();
						console.log(res);
						if(res.data.code == 200) {
							that.list = that.list.concat(res.data.data.list)
						}
					},
					fail(res) {
						console.log(res)
					}
				})
			}
		}
	}
</script>

<style>
	.list-item {
		box-sizing: border-box;
		padding: 0 46upx;
		height: 120upx;
		background-color: #fff;
		border-bottom: 1px solid #e9e9e9;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.list-item .detail {
		
	}
	.list-item .detail .content {
		font-size: 28upx;
		line-height: 50upx;
	}
	.list-item .detail .createTime {
		font-size: 20upx;
		color: #a2a2a2;
	}
	.list-item .money {
		font-size: 34upx;
		color: #E9CD9B;
	}
</style>
