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
				<view class="avatar"><image src="/static/imgs/default.png"  ></image></view>
				<view class="nickName">{{item.nickName}}</view>
				<view class="level" v-if="item.level == 1"><text>一级粉丝</text></view>
				<view class="level" v-if="item.level == 2"><text>二级粉丝</text></view>						
			</view>
			
		</view>
		
	</view>
</template>

<script>
	export default {
		data(){
			return {
				status: -1,
				nav:[
					{
						status: -1,
						label: '全部粉丝'
					},
					{
						status: 0,
						label: '一级粉丝'
					},
					{
						status: 1,
						label: '二级粉丝'
					}										
				],
				list: [
					{
						avatar: '',
						nickName: '脱俗仙子谈无欲',
						level: 1
					},
					{
						avatar: '',
						nickName: '脱俗仙子谈无欲',
						level: 1
					},
					{
						avatar: '',
						nickName: '脱俗仙子谈无欲',
						level: 1
					}										
				]
			}
		},
		methods:{
			// nav切换
			changeStatus (e) {
				let that = this;
				let status = e.currentTarget.dataset.status;
				console.log(status);
				that.status = status;
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
