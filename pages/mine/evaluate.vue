<template>
	<view class="container">
		<view class="info">
			<view class="pic">
				<image src="../../static/imgs/default.png"></image>
			</view>
			<view class="desc">
				<view class="title">{{title}}</view>
				<view class="rate">
					<image @tap="selStar" :data-i='1' :src="[rate >= 1 ? '/static/imgs/starFilled.png' : '/static/imgs/star.png']"></image>
					<image @tap="selStar" :data-i='2' :src="[rate >= 2 ? '/static/imgs/starFilled.png' : '/static/imgs/star.png']"></image>
					<image @tap="selStar" :data-i='3' :src="[rate >= 3 ? '/static/imgs/starFilled.png' : '/static/imgs/star.png']"></image>
					<image @tap="selStar" :data-i='4' :src="[rate >= 4 ? '/static/imgs/starFilled.png' : '/static/imgs/star.png']"></image>
					<image @tap="selStar" :data-i='5' :src="[rate >= 5 ? '/static/imgs/starFilled.png' : '/static/imgs/star.png']"></image>
				</view>
			</view>
		</view>
		<view class="content">
			<textarea auto-height="true" maxlength="100" placeholder="输入您的评价吧(5-100字)" v-model="evaluateText"></textarea>
			<view class="photos" @tap="uploadImage">
				<view class="photo-list" v-for="(item,index) in photoList" :key='index'>
					<image :src="imgUrl + item"></image>
				</view>
				<view v-if="photoList.length == 0" class="btn">
					<image src="/static/imgs/camera.png"></image>
				</view>

			</view>
		</view>
		<view class="sub" v-if="!false"><button>发表评价</button></view>
	</view>
</template>

<script>
	const app = require('../../App.vue');
	export default {
		data() {
			return {
				imgUrl: app.default.globalData.imgUrl,
				uniIconSize: 24,
				pic: '',
				title: '世事如棋，乾坤莫测，笑尽英雄啊',
				rate: 0,
				evaluateText: '',
				photoList: [],
				isReview: false
			}
		},
		onLoad(options) {
			let that = this;
			uni.getSystemInfo({
				success: function(res) {
					that.uniIconSize = (24 / 750) * Number(res.windowWidth)
				}
			});
		},
		methods: {
			// 评论星星
			selStar(e) {
				let that = this;
				that.rate = Number(e.currentTarget.dataset.i)
			},
			// 拍照选择图片
			uploadImage: function() {
				let that = this;
				// let url = that.data.url;
				let arr = [];
				if (!that.isReview) {
					uni.chooseImage({
						count: 3,
						sizeType: ['original', 'compressed'],
						sourceType: ['album', 'camera'],
						success: function(res) {
							// tempFilePath可以作为img标签的src属性显示图片
							const tempFilePaths = res.tempFilePaths;
							console.log(tempFilePaths);
							//启动上传等待中...  
							wx.showToast({
								title: '正在上传...',
								icon: 'loading',
								mask: true,
								duration: 10000
							})
							var uploadImgCount = 0;
							for (var i = 0, h = tempFilePaths.length; i < h; i++) {
// 								wx.uploadFile({
// 									url: url,
// 									filePath: tempFilePaths[i],
// 									name: 'fileName',
// 									formData: {
// 										'imgIndex': i
// 									},
// 									header: {
// 										"Content-Type": "multipart/form-data"
// 									},
// 									success: function(res) {
// 
// 										uploadImgCount++;
// 										var data = JSON.parse(res.data);
// 										arr.push(data.data.fileName);
// 										that.setData({
// 											picList: arr
// 										})
// 										//如果是最后一张,则隐藏等待中  
// 										if (uploadImgCount == tempFilePaths.length) {
// 											wx.hideToast();
// 										}
// 									},
// 									fail: function(res) {
// 										wx.hideToast();
// 										wx.showModal({
// 											title: '错误提示',
// 											content: '上传图片失败',
// 											showCancel: false,
// 											success: function(res) {}
// 										})
// 									}
// 								});
							}
						},
					})
				}
			}
		}
	}
</script>

<style>
	.info {
		padding: 36upx 46upx;
		display: flex;
		align-items: stretch;
		justify-content: space-between;
		background-color: #fff;
		border-bottom: 1px solid #e9e9e9;
	}

	.info .pic,
	.info .pic image {
		width: 150upx;
		height: 150upx;
		background-color: #f8f8f8;
		border-radius: 50%;
	}

	.info .desc {
		padding: 0 20upx;
		flex: 1;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.info .desc .title {
		font-size: 28upx;
		height: 80upx;
		line-height: 40upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
	}

	.info .desc .rate {
		height: 50upx;
		display: flex;
		align-items: center;
	}

	.info .desc .rate image {
		width: 50upx;
		height: 50upx;
	}

	/* 评论输入 */
	.content {
		padding: 36upx 46upx;
		background-color: #fff;
		font-size: 28upx;
		display: flex;
		flex-direction: column;
	}

	.content textarea {
		width: 100%;
		height: 300upx !important;
		line-height: 50upx !important;
	}

	.content .photos {
		margin: 30upx 0;
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
	}

	.content .photos view {
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.content .photos view,
	.content .photos view image {
		width: 200upx;
		height: 200upx;
	}

	.content .photos view.btn {
		border: 1px solid #e9e9e9;
	}

	.content .photos view.btn image {
		width: 100upx;
		height: 100upx;
	}

	/* 提交按钮 */
	.sub {
		margin: 50upx;
	}

	.sub button {
		font-size: 28upxs;
		color: #fff;
		background-color: #313131;
	}
</style>
