<template>
	<scroll-view class="ccontainer">
		<!-- 状态栏 -->
		<view class="status" :style="{position:headerPosition}"></view>
		<view class="header" :style="{position:headerPosition}">
			<view class="input-box">
				<image class="icon-search" src="/static/imgs/icon-search.png" mode=""></image>
				<input placeholder="请输入关键词" focus confirm-type="search" v-model="searchBox" placeholder-style="color:#c0c0c0;"/>
			</view>
			<view class="cancel" @tap="back()">
				取消
			</view>
		</view>
		
		<!-- 产品列表 -->
		<view class="lists main">
			<view class="product-item">
				<image class="product-img" src="/static/imgs/product.png" mode="" lazy-load="true"></image>
				<view class="product-title">
                    积分兑换商品  TNN玻尿酸补水蚕丝面膜1盒    
				</view>
				<view class="product-price">
					<view class="product-price-num">
						500积分
						<view class="product-sell-num">
							已兑换32435件
						</view>
					</view>
					<image class="product-icon-add" src="../../static/imgs/icon-add.png" mode=""></image>
				</view>
			</view>
			<view class="product-item">
				<image class="product-img" src="/static/imgs/product.png" mode="" lazy-load="true"></image>
				<view class="product-title">
                    积分兑换商品  TNN玻尿酸补水蚕丝面膜1盒    
				</view>
				<view class="product-price">
					<view class="product-price-num">
						￥399
						<view class="product-sell-num">
							已售出32435件
						</view>
					</view>
					<image class="product-icon-add" src="../../static/imgs/icon-add.png" mode=""></image>
				</view>
			</view>
			<view class="product-item">
				<image class="product-img" src="/static/imgs/product.png" mode="" lazy-load="true"></image>
				<view class="product-title">
                    积分兑换商品  TNN玻尿酸补水蚕丝面膜1盒    
				</view>
				<view class="product-price">
					<view class="product-price-num">
						500积分
						<view class="product-sell-num">
							已兑换32435件
						</view>
					</view>
					
					<image class="product-icon-add" src="../../static/imgs/icon-add.png" mode=""></image>
				</view>
			</view>
			<view class="product-item">
				<image class="product-img" src="/static/imgs/product.png" mode="" lazy-load="true"></image>
				<view class="product-title">
                    积分兑换商品  TNN玻尿酸补水蚕丝面膜1盒    
				</view>
				<view class="product-price">
					<view class="product-price-num">
						￥399
						<view class="product-sell-num">
							已售出32435件
						</view>
					</view>
					
					<image class="product-icon-add" src="../../static/imgs/icon-add.png" mode=""></image>
				</view>
			</view>
		</view>
	</scroll-view>
</template>


<script>
	export default {
		data() {
			return {
				showCategoryIndex: 0,
				headerPosition: "fixed",
				searchBox: "TNT面膜"
			}
		},
		onPageScroll(e) {
			//兼容iOS端下拉时顶部漂移
			if (e.scrollTop >= 0) {
				this.headerPosition = "fixed";
			} else {
				this.headerPosition = "absolute";
			}
		},
		onLoad() {
			
		},
		methods: {
			//取消返回上一页 
			back(){
				uni.navigateBack({
					delta: 1
				});
			},
			//搜索
			toSearch() {
				console.log(123)
			}
		}

	}
</script>

<style lang="scss">
	.status {
		width: 100%;
		height: 0;
		position: fixed;
		z-index: 10;
		background-color: #fff;
		top: 0;
		/*  #ifdef  APP-PLUS  */
		height: var(--status-bar-height); //覆盖样式
		/*  #endif  */

	}

	.header {
		width: 92%;
		padding: 0 4%;
		height: 100upx;
		display: flex;
		align-items: center;
		position: fixed;
		top: 0;
		z-index: 10;
		background-color: #fff;
		/*  #ifdef  APP-PLUS  */
		top: var(--status-bar-height);
		/*  #endif  */

		.input-box {
			width: 100%;
			height: 60upx;
			background-color: #FFF;
			border:1upx solid #CBCBCB;
			border-radius: 30upx;
			position: relative;
			display: flex;
			align-items: center;
			.icon-search{
				width: 30upx;
				height: 32upx;
				margin:0 0 0 20upx;
			}
			input {
				padding-left: 28upx;
				height: 28upx;
				font-size: 28upx;
				width:95%;
			}
		}

		.cancel{
			width: 70upx;
			padding: 0 15upx;
			font-size: 28upx;
			color: #A2A2A2;
		}
	}

/* 产品列表 */
.lists{
	margin:150upx 0 0 0;
	padding: 0 32upx 30upx 46upx;
	display: flex;
	flex-flow: row wrap;
	justify-content: flex-start;
}
.product-item{
	width:48%;
	box-sizing: border-box;
	margin:0 2% 30upx 0;
}
.product-img{
	width: 100%;
	height: 326upx;
	display: flex;
	flex-flow: column nowrap;
	justify-content: center;
}
.product-price{
	padding: 0 12upx;
	box-sizing: border-box;
	display: flex;
	flex-flow: row nowrap;
	justify-content: space-between;
	align-items: center;
}
.product-title{
	line-height: 34upx;
	font-size: 28upx;
	padding: 12upx;
}
.product-price-num{
	color: #FF0000;
	font-size: 26upx;
	
}
.product-sell-num{
	color: #B9B9B9;
	font-size: 20upx;
}
.product-icon-add{
	width: 50upx;
	height: 50upx;
}
</style>
