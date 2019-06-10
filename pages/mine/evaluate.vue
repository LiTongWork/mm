<template>
	<view class="container">
		<view class="info">
<!-- 			<view class="pic">
				<image src="../../static/imgs/default.png"></image>
			</view> -->
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
			<textarea auto-height="true" maxlength="100" placeholder="输入您的评价吧(5-100字)" :readonly=" isReview == 'false' ? '' : 'readonly' " v-model="evaluateText"></textarea>
			<view class="photos" @tap="uploadImage">
				<view class="photo-list" v-for="(item,index) in photoList" :key='index'>
					<image :src="imgUrl + item"></image>
				</view>
				<view v-if="photoList.length == 0 && isReview == 'false' " class="btn">
					<image src="/static/imgs/camera.png"></image>
				</view>

			</view>
		</view>
		<view class="sub" v-if="isReview == 'false' "><button @tap="sub">发表评价</button></view>
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
				title: '',
				rate: 0,
				evaluateText: '',
				photoList: [],
				isReview: 'false',
				indentId: '',
				status: ''
			}
		},
		onLoad(options) {
			let that = this;
			that.indentId = options.indentId;
			that.isReview = options.isReview;
			that.status = options.status;
			if (that.isReview == 'true') {
				that.getDetail();
			}
		},
		methods: {
			// 获取评价内容
			getDetail(){
				let that = this;
				let params = {
					indentId: that.indentId
				}
				uni.request({
					url: app.default.globalData.baseUrl + "/api/User/ReviewDetails",
					method: "POST",
					header: {
						'content-type': 'application/json',
						'auth': app.default.globalData.token
					},
					data: params,
					dataType: "json",
					success(res) {
						console.log(res);
						if (res.data.code == 200) {
							that.rate = res.data.data.star;
							that.evaluateText = res.data.data.content;
							that.photoList = res.data.data.imgList;
						}
					},
					fail(res){
						console.log(res)
					}
				})					
			},
			// 评论星星
			selStar(e) {
				let that = this;
				if (that.isReview == 'false') {
					that.rate = Number(e.currentTarget.dataset.i)
				}
			},
			// 拍照选择图片
			uploadImage: function() {
				let that = this;
				let url = app.default.globalData.baseUrl + '/api/Upload/UploadImg';
				let arr = [];
				if (that.isReview == 'false') {
					uni.chooseImage({
						count: 3,
						sizeType: ['original', 'compressed'],
						sourceType: ['album', 'camera'],
						success: function(res) {
							// tempFilePath可以作为img标签的src属性显示图片
							const tempFilePaths = res.tempFilePaths;
							// console.log(tempFilePaths);
							//启动上传等待中...  
							uni.showToast({
								title: '正在上传...',
								icon: 'loading',
								mask: true,
								duration: 10000
							})
							var uploadImgCount = 0;
							for (var i = 0, h = tempFilePaths.length; i < h; i++) {
								uni.uploadFile({
									url: url,
									filePath: tempFilePaths[i],
									name: 'fileName',
									formData: {
										'imgIndex': i
									},
									header: {
										"Content-Type": "multipart/form-data"
									},
									success: function(res) {

										uploadImgCount++;
										var data = JSON.parse(res.data);
										arr.push(data.data.fileName);
										that.photoList = arr;
										//如果是最后一张,则隐藏等待中  
										if (uploadImgCount == tempFilePaths.length) {
											uni.hideToast();
										}
									},
									fail: function(res) {
										uni.hideToast();
										uni.showModal({
											title: '错误提示',
											content: '上传图片失败',
											showCancel: false,
											success: function(res) {}
										})
									}
								});
							}
						},
					})
				}
			},
			// 发布评价
			sub () {
				let that = this;
				console.log(that.photoList)
				let params = {
					indentId: that.indentId,
					star: that.rate,
					content: that.evaluateText,
					imgList: that.photoList.length 
				}
				if (params.star == 0) {
					uni.showToast({
						title: '请先评价星级',
						icon: 'none',
						mask: true,
						duration: 1500
					})
				} else if (params.content.length == 0) {
					uni.showToast({
						title: '请输入评价内容',
						icon: 'none',
						mask: true,
						duration: 1500
					})					
				} else if (params.content.length < 5) {
					uni.showToast({
						title: '评价内容不少于五个字',
						icon: 'none',
						mask: true,
						duration: 1500
					})					
				} else {
					uni.request({
						url: app.default.globalData.baseUrl + "/api/User/AddReview",
						method: "POST",
						header: {
							'content-type': 'application/json',
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
							if (res.data.code == 200) {
								setTimeout(() => {
									uni.switchTab({
										url: '/pages/mine/mine'
									})
									// uni.redirectTo({
									// 	url: `/pages/mine/order?status=${that.status}`
									// })
								},1500)
							}
						},
						fail(res){
							console.log(res)
						}
					})						
				}
				console.log(params);
				
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
		/* justify-content: ; */
	}

	.content .photos view {
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-right: 10upx;
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
