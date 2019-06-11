<template>
	<view class="container">
		<view class="header-nav">
			<view class="nav-list" :class="{active: status == item.status }" v-for="(item,index) in nav" :key='index' :data-status='item.status' @tap="changeStatus">
				<text>{{item.label}}</text>
			</view>
		</view>
		<!-- 内容列表 -->
		<view class="list">
			<view class="list-item" v-for="(item,index) in list" :key='index'>
				<view class="avatar"><image :src="item.headImg"></image></view>
				<view class="nickName">{{item.nickName}}</view>
				<view class="level" v-if="item.level == 1"><text>一级粉丝</text></view>
				<view class="level" v-if="item.level == 2"><text>二级粉丝</text></view>						
			</view>
			<view class="noData" v-if="list.length == 0">暂无数据</view>
		</view>
		
	</view>
</template>

<script>
	const app = require("../../App.vue");
	export default {
		data(){
			return {
				status: 0,
				url: '/api/Distribution/UserFirstList',
				nav:[
					{
						status: 0,
						label: '一级粉丝'
					},
					{
						status: 1,
						label: '二级粉丝'
					}										
				],
				list: [],
				page: 1,
				rows: 12				
			}
		},
		onLoad() {
			let that = this;
			that.getList(that.url);
		},
		onPullDownRefresh() {
			let that = this;
			that.list = [];
			that.page = 1;
			that.getList(that.url);
		},
		onReachBottom() {
			let that = this;
			that.page++;
			that.getList(that.url);
		},
		methods:{
			// nav切换
			changeStatus (e) {
				let that = this;
				let status = e.currentTarget.dataset.status;
				if (that.status != status) {
					that.status = status;
					that.list = [];
					that.page = 1;
					if(that.status == 0) {
						that.url = '/api/Distribution/UserFirstList'
					} else if (that.status == 1) {
						that.url = '/api/Distribution/UserSecondList'
					} 
					that.getList(that.url);
				}

			},
			// 获取列表
			getList(url){
				let that = this;
				uni.showLoading();
				let params = {
					page: that.page,
					rows: that.rows
				}
				uni.request({
					url: app.default.globalData.baseUrl + url,
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: "json",
					success(res) {
						console.log(res);
						uni.hideLoading();
						uni.stopPullDownRefresh();
						if(res.data.code == 200) {
							that.list = that.list.concat(res.data.data.list);
							for(var i=0;i<that.list.length;i++) {
								that.list[i].headImg = JSON.parse(res.data.data.list[i].headImg)
							}
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
		padding-top: 88upx;
	}
	.header-nav {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 88upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		background-color: #fff;
		border-bottom: 2px solid #fff;
	}
	.header-nav .nav-list {
		flex: 1;
		text-align: center;
	}
	.header-nav .nav-list text {
		display: inline-block;
		height: 88upx;
		line-height: 88upx;
		border-bottom: 2px solid #fff;
		font-size: 28upx;
	}
	.header-nav .nav-list.active text {
		color: #E9CD9B;
		border-bottom: 2px solid #E9CD9B;
	}
	/* 内容列表 */
	.list {
		padding: 36upx 46upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		
	}
	.list-item {
		box-sizing: border-box;
		width: 214upx;
		padding: 20upx;
		background-color: #fff;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-between;
	}
	.list-item .avatar {
		background-color: #f8f8f8;
		border-radius: 50%;
		overflow: hidden;
	}
	.list-item .avatar, .list-item .avatar image {
		width: 120upx;
		height: 120upx;		
	}
	.list-item .nickName {
		line-height: 50upx;
		font-size: 24upx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}
	.list-item .level {
		display: inline-block;
		padding: 6upx 10upx;
		font-size: 24upx;
		color: #fff;
		background-color: #E9CD9B;
		border-radius: 6upx;
	}
</style>
