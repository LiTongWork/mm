<template>
	<view>
		<view class="status" :style="{position:headerPosition,top:statusTop}"></view>
		<view class="header checkbox-box" :style="{position:headerPosition,top:headerTop}">
			<view class="checkbox" @tap="allSelect()">
				<view :class="[isAllselected?'on':'']">
					<uni-icon type="checkmarkempty" size="20" color="#fff"></uni-icon>
				</view>
			</view>
			<view class="title">平台自选</view>
			<!-- <image class="icon-delete" src="/static/imgs/icon-delete.png" mode="" @tap="deleteList"></image> -->
		</view>
		<!-- 占位 -->
		<view class="place"></view>
		<!-- 商品列表 -->
		<view class="goods-list">
			<view class="tis" v-if="goodsList.length==0">购物车是空的哦~</view>
			<view class="row" v-for="(row,index) in goodsList" :key="index">
				<!-- 删除按钮 -->
				<view class="menu" @click="deleteGoods(row.goodsId,index)">
					<image class="icon-delete" src="/static/imgs/icon-delete-white.png" mode=""></image>
				</view>
				<!-- 商品 -->
				<view class="carrier" :class="[theIndex==index?'open':oldIndex==index?'close':'']" @touchstart="touchStart(index,$event)"
				 @touchmove="touchMove(index,$event)" @touchend="touchEnd(index,$event)">
					<!-- checkbox -->
					<view class="checkbox-box" @tap="selected(index)">
						<view class="checkbox">
							<view :class="[row.selected?'on':'']">
								<uni-icon type="checkmarkempty" size="20" color="#fff"></uni-icon>
							</view>
						</view>
					</view>
					<!-- 商品信息 -->
					<view class="goods-info" @tap="toGoods(row)">
						<view class="img">
							<image :src="imgUrl+row.goodsImg"></image>
						</view>
						<view class="info">
							<view class="title">{{row.goodsName}}</view>
							<view class="spec">运费:{{row.freight}}</view>
							<view class="price-number">
								<view class="price" v-if="row.payMethod==1">￥{{row.currentPice}}</view>
								<view class="price" v-if="row.payMethod==2">{{row.currentPice}}积分</view>
								<view class="number">
									<view class="sub" @tap.stop="sub(index)">
										<view class="jian">-</view>
									</view>
									<view class="input" @tap.stop="discard">
										<input type="number" v-model="row.number" @input="sum($event,index)" />
									</view>
									<view class="add" @tap.stop="add(index)">
										<view class="jia">+</view>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 脚部菜单 -->
		<view class="footer" :style="{bottom:footerbottom}">
			<view class="checkbox-box" @tap="allSelect">
				<view class="checkbox">
					<view :class="[isAllselected?'on':'']">
						<uni-icon type="checkmarkempty" size="20" color="#fff"></uni-icon>
					</view>
				</view>
				<view class="text">全选</view>
			</view>
			<!-- <view class="delBtn" @tap="deleteList" v-if="selectedList.length>0">删除</view> -->
			<view class="settlement">
				<view class="sum">合计:<text class="money">{{sumPrice}}</text></view>
				<!-- <view class="sum">合计:<text class="money" v-if="payMethod==2">{{sumPrice}}积分</text></view> -->
				<!-- <view class="sum" v-if="payMethod==2">合计:<text class="money">{{sumPrice}}积</text></view> -->
				<view>
					<view class="btn" @tap="toConfirmation" :itemlength="selectedList.length">立即购买</view>
				</view>
			</view>
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
				freight:"0",
				sumPrice: '0.00',
				headerPosition: "fixed",
				headerTop: null,
				statusTop: null,
				selectedList: [],
				isAllselected: false,
				goodsList: [],
				goodsChecked: [], //选中的商品id,number列表
				//控制滑动效果
				theIndex: null,
				oldIndex: null,
				isStop: false,
				imgUrl: app.default.globalData.imgUrl, //图片拼接位置
				payMethod: 1, //支付方式
				jifen: false, //积分方式
				qian: false //钱方式
			}
		},
		onPageScroll(e) {
			//兼容iOS端下拉时顶部漂移
			this.headerPosition = e.scrollTop >= 0 ? "fixed" : "absolute";
			this.headerTop = e.scrollTop >= 0 ? null : 0;
			this.statusTop = e.scrollTop >= 0 ? null : -this.statusHeight + 'px';
		},
		//下拉刷新，需要自己在page.json文件中配置开启页面下拉刷新 "enablePullDownRefresh": true
		onPullDownRefresh() {
			this.jifen = false;
			this.qian  = false;
			this.isAllselected = false;
			this.goodsItem()
			setTimeout(function() {
				uni.stopPullDownRefresh();
			}, 1000);
		},
		onLoad() {
			//兼容H5下结算条位置
			// #ifdef H5
			this.footerbottom = document.getElementsByTagName('uni-tabbar')[0].offsetHeight + 'px';
			// #endif
			// #ifdef APP-PLUS
			this.statusHeight = plus.navigator.getStatusbarHeight();
			// #endif
			this.goodsItem();
			this.isAllselected = false;
		},
		onShow() {
			this.sumPrice = 0;
			this.freight = 0
			this.jifen = false;
			this.qian  = false;
			this.isAllselected = false;
			this.goodsChecked = [];
			this.goodsChecked = [];
			this.goodsItem()
		},
		methods: {
			//购物车列表
			goodsItem() {
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						"auth": app.default.globalData.token
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Store/GetCarGoods",
					success(res) {
						that.goodsList = res.data.data;
						that.theIndex = null;
						that.oldIndex = null;
					}
				})
			},
			//加入商品 参数 goods:商品数据
			joinGoods(goods) {
				/*
				 * 这里只是展示一种添加逻辑，模板并没有做从其他页面加入商品到购物车的具体动作，
				 * 在实际应用上，前端并不会直接插入记录到goodsList这一个动作，一般是更新列表和本地列表缓存。
				 * 一般商城购物车的增删改动作是由后端来完成的,
				 * 后端记录后返回前端更新前端缓存
				 */
				let len = this.goodsList.length;
				let isFind = false; //是否找到ID一样的商品
				for (let i = 0; i < len; i++) {
					let row = this.goodsList[i];
					if (row.id == goods.id) { //找到商品一样的商品
						this.goodsList[i].number++; //数量自增
						isFind = true; //找到一样的商品
						break; //跳出循环
					}
				}
				if (!isFind) {
					//没有找到一样的商品，新增一行到购物车商品列表头部
					this.goodsList[i].unshift(goods);
				}
			},
			//控制左滑删除效果-begin
			touchStart(index, event) {
				//多点触控不触发
				if (event.touches.length > 1) {
					this.isStop = true;
					return;
				}
				this.oldIndex = this.theIndex;
				this.theIndex = null;
				//初始坐标
				this.initXY = [event.touches[0].pageX, event.touches[0].pageY];
			},
			touchMove(index, event) {
				//多点触控不触发
				if (event.touches.length > 1) {
					this.isStop = true;
					return;
				}
				let moveX = event.touches[0].pageX - this.initXY[0];
				let moveY = event.touches[0].pageY - this.initXY[1];

				if (this.isStop || Math.abs(moveX) < 5) {
					return;
				}
				if (Math.abs(moveY) > Math.abs(moveX)) {
					// 竖向滑动-不触发左滑效果
					this.isStop = true;
					return;
				}

				if (moveX < 0) {
					this.theIndex = index;
					this.isStop = true;
				} else if (moveX > 0) {
					if (this.theIndex != null && this.oldIndex == this.theIndex) {
						this.oldIndex = index;
						this.theIndex = null;
						this.isStop = true;
						setTimeout(() => {
							this.oldIndex = null;
						}, 150)
					}
				}
			},
			touchEnd(index, $event) {
				//结束禁止触发效果
				this.isStop = false;
			},
			//控制左滑删除效果-end
			//商品跳转
			toGoods(e) {
				uni.showToast({
					title: '商品' + e.id,
					icon: "none"
				});
				uni.navigateTo({
					url: '../goods/goods'
				});
			},
			//跳转确认订单页面
			toConfirmation() {
				let that = this;
				let len = that.goodsList.length;
				let payMethod = 0
				for (let i = 0; i < len; i++) {
					if (that.goodsList[i].selected) {
						let goods = {
							goodsId: that.goodsList[i].goodsId,
							number: that.goodsList[i].number
						}
						that.payMethod=that.goodsList[i].payMethod
						console.log(" that.goodsList[i].goodsId", that.goodsList[i].goodsId)
						that.goodsChecked.push(goods)
					}
				}
				// console.log(that.payMethod)
				if (that.qian && that.jifen) {
					uni.showToast({
						duration: 2000,
						title: '请选择相同支付的方式的商品',
						icon: 'none'
					});
					that.isAllselected = false;
					that.jifen = false;
					that.qian = false;
					that.goodsItem();
					that.goodsChecked=[]
					return;
				}
				if (that.goodsChecked.length < 1) {
					uni.showToast({
						duration: 2000,
						title: '请选择商品结算',
						icon: 'none'
					});
					return;
				}
				console.log(that.goodsChecked)
				uni.request({
					header: {
						'content-type': 'application/json',
						"auth": app.default.globalData.token
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/User/PrepaidOrder",
					data: {
						"goodsChecked": that.goodsChecked
					},
					success(res) {
						console.log(res.data)
						var goodsChecked = JSON.stringify(that.goodsChecked)
						if (res.data.code == 200) {
							if(that.payMethod==2) {
								uni.navigateTo({
									url: "../order/confirmation?goodsChecked=" + goodsChecked + "&sumPrice=" + that.freight
								})
							}else{
								uni.navigateTo({
									url: "../order/confirmation?goodsChecked=" + goodsChecked + "&sumPrice=" + that.sumPrice
								})
							}
							
						} else {
							uni.showToast({
								duration: 2000,
								title: res.data.message,
								icon: "none",
							});
							return;
						}
					}
				})
			},
			//删除商品
			deleteGoods(id, index) {
				console.log("index", index)
				let that = this;
				uni.request({
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						"auth": app.default.globalData.token
					},
					method: "POST",
					dataType: 'json',
					url: app.default.globalData.baseUrl + "/api/Store/RemoveCar",
					data: {
						goodsId: id
					},
					success(res) {
						that.goodsItem();
					}
				})
			},
			// 选中商品
			selected(index) {
				this.goodsList[index].selected = this.goodsList[index].selected ? false : true;
				let i = this.selectedList.indexOf(this.goodsList[index].id);
				i > -1 ? this.selectedList.splice(i, 1) : this.selectedList.push(this.goodsList[index].id);
				this.isAllselected = this.selectedList.length == this.goodsList.length;
				this.sum();
				if (this.goodsList[index].payMethod==1) {
					this.qian = !this.qian;
					console.log("this.qian",this.qian)
				}
				if (this.goodsList[index].payMethod==2) {
					this.jifen = !this.jifen;
					console.log("this.jifen",this.jifen)
				}
			},
			//全选
			allSelect() {
				let len = this.goodsList.length;
				let arr = [];
				for (let i = 0; i < len; i++) {
					this.goodsList[i].selected = this.isAllselected ? false : true;
					arr.push(this.goodsList[i].id);
					if(this.goodsList[i].selected) {
						this.jifen = true;
						this.qian  =true;
					}else{
						this.jifen = false;
						this.qian  =false;
						this.isAllselected = false;
					}
				}
				this.selectedList = this.isAllselected ? [] : arr;
				this.isAllselected = this.isAllselected || this.goodsList.length == 0 ? false : true;
				this.sum();
			},
			// 减少数量
			sub(index) {
				if (this.goodsList[index].number <= 1) {
					return;
				}
				this.goodsList[index].number--;
				this.sum();
			},
			// 增加数量
			add(index) {
				this.goodsList[index].number++;
				this.sum();
			},
			// 合计
			sum(e, index) {
				this.sumPrice = 0;
				let len = this.goodsList.length;
				for (let i = 0; i < len; i++) {
					if (this.goodsList[i].selected) {
						if(this.goodsList[i].payMethod==1) {
							if (e && i == index) {
								this.sumPrice = this.sumPrice + (e.detail.value * this.goodsList[i].currentPice + this.goodsList[i].freight);
							} else {
								this.sumPrice = this.sumPrice + (this.goodsList[i].number * this.goodsList[i].currentPice + this.goodsList[i].freight);
							}
						}else{
							if (e && i == index) {
								this.sumPrice = this.sumPrice + (e.detail.value * this.goodsList[i].currentPice);
							} else {
								this.sumPrice = this.sumPrice + (this.goodsList[i].number * this.goodsList[i].currentPice);
							}
						}
						
					}
				}
				this.sumPrice = this.sumPrice.toFixed(2);
			}
		}
	}
</script>
<style lang="scss">
	page {
		position: relative;
	}

	@font-face {
		font-family: "HMfont-home";
		src: url('data:application/x-font-woff2;charset=utf-8;base64,d09GMgABAAAAAAOEAAsAAAAAB7wAAAM1AAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHEIGVgCDHAqDAIJNATYCJAMQCwoABCAFhG0HPhu1BsiOw7jha84s8T+CgOyq6unevTlAeAkRhAhQBjj8eJfmMg/oB5whSgaFspy7AqbpwE3Py3/j9j+X07uGyFHr21lua9HYi9rUCzDeGtBYmw4o0AJJ0BvGLmiJxyEAC3yIQhSVPL9BF4U5TgBi7ChtQzfjQKFYWEdgrFiqEBsxoGurtMfAhuD74g8JQgcNg8ScWjf8xoPz7/nfD8iFpoV0eQi86XQAWwUSiAIUiJGVhgEaUSAKiYXau0kKoKOjwVRthd8PmExIBRvmWH95INCQIIyYqVYAJKGghO/5Dg4B3wtxaPD9QAoS4IOM1gEzYBbAMkT16iIQGPxsopS5ScAcK23LiaBDpzyk1aGLHtsvhVrL7adDD55016wPXnDfcr7T3lcE2TVcP2fNhZM7WeiEhVp44vx9C5vVD6+eEgaLTZvEot3HL1lawlnXLK3XnISDllbWwmrmGjlRLdxq2bpme1RAeqXtzWh6g0F//wCCnRiN2diwr2+Yse3iEs20g05Q5tT0BikOXwKf79vsoEvb6p6xjVhHP92nwDJmX+9hwsf26dXbPiNvZ/98x67/+5T+ev0aWxbXCwOX41zLxia+O73B6Y57fN1u6GvN+Zmbc8XITigTAcB0Rl6X8QB4hDgEpoXypZz2q37D5FVnVa9t1n/dUgHw8uKMq9BmWqjoJoP3zevM+zG8jmVKE5hKpRShw9WTFZW04D7TscAC+B0RcOD9FHLnzqkg0HFOQMMMb5DoBKGKGgUGLEkCIzq5YEEkpastcWQyDYQyByJYBkBgx2nQsOEaSOx4girqOzDgwj8wYickWNAiHLe0JESMsiicTLaQh84PBm6WKlst/TD9hXQV25wWBLQP4jJw435MXD58RynxFDPKT30golBxluAtOI3iOMOcs5Bc2fFF8sPd2OFVBO24WQJ9CmEIG4HwQAb4ARmFS0aK8qoL/cLnXyA0FTE23FBSZf+AYEqCzpF4YojrgN5J004lt/LKFD7RHDAVQYEMh8lIQErAICImJgOZWD0ohHARdhg5wJdzSKxVaXhX6c7y8uQN1wAWmLOU0IQUShiQo0fZTceadqaMdP2uXwEAAAAA') format('woff2');
	}

	.icon {
		font-family: "HMfont-home" !important;
		font-size: 60upx;
		font-style: normal;
		color: #000000;

		&.jia {
			&:before {
				content: "\e641";
			}
		}

		&.jian {
			&:before {
				content: "\e643";
			}
		}

		&.shanchu {
			&:before {
				content: "\e6a4";
			}
		}

	}

	.checkbox-box {
		display: flex;
		align-items: center;

		.checkbox {
			width: 36upx;
			height: 36upx;
			border-radius: 100%;
			border: solid 2upx #C0C0C0;
			display: flex;
			justify-content: center;
			align-items: center;

			.on {
				width: 36upx;
				height: 36upx;
				border-radius: 100%;
				background-color: #000;
			}
		}

		.text {
			font-size: 28upx;
			margin-left: 10upx;
		}
	}

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
		border-bottom: 1upx solid #F3F3F3;

		.checkbox {
			margin: 0 20upx 0 20upx;
		}

		.title {
			font-size: 30upx;
		}

		.icon-delete {
			width: 36upx;
			height: 37upx;
			// align-self: center;
			margin-left: auto;
		}

	}

	.place {

		background-color: #ffffff;
		height: 100upx;
		/*  #ifdef  APP-PLUS  */
		margin-top: var(--status-bar-height);
		/*  #endif  */
	}

	.goods-list {
		width: 100%;
		padding: 20upx 0 120upx 0;

		.tis {
			width: 100%;
			height: 60upx;
			display: flex;
			justify-content: center;
			align-items: center;
			font-size: 32upx;
		}

		.row {
			width: calc(92.5%);
			height: calc(22vw + 40upx);
			margin: 20upx auto;
			border-radius: 15upx;
			// box-shadow: 0upx 5upx 20upx rgba(0,0,0,0.1);
			display: flex;
			align-items: center;
			position: relative;
			overflow: hidden;
			z-index: 4;
			border: 0;

			.menu {
				.icon-delete {
					width: 60upx;
					height: 60upx;
				}

				.icon {
					color: #fff;
					// font-size: 25upx;
				}

				position: absolute;
				width: 30%;
				height: 99%;
				right: 12upx;
				// right: 0;//未修改前
				//height: 100%;//未修改前
				display: flex;
				justify-content: center;
				align-items: center;
				background-color: red;
				color: #fff;
				z-index: 2;
			}

			.carrier {
				@keyframes showMenu {
					0% {
						transform: translateX(0);
					}

					100% {
						transform: translateX(-30%);
					}
				}

				@keyframes closeMenu {
					0% {
						transform: translateX(-30%);
					}

					100% {
						transform: translateX(0);
					}
				}

				&.open {
					animation: showMenu 0.25s linear both;
				}

				&.close {
					animation: closeMenu 0.15s linear both;
				}

				background-color: #fff;

				.checkbox-box {
					padding-left: 20upx;
					flex-shrink: 0;
					height: 22vw;
					margin-right: 20upx;
				}

				position: absolute;
				width: 100%;
				padding: 0 0;
				height: 100%;
				z-index: 3;
				display: flex;
				align-items: center;

				.goods-info {
					width: 100%;
					display: flex;
					padding-right: 20upx;

					.img {
						width: 22vw;
						height: 22vw;
						border-radius: 10upx;
						overflow: hidden;
						flex-shrink: 0;
						margin-right: 10upx;

						image {
							width: 22vw;
							height: 22vw;
						}
					}

					.info {
						width: 100%;
						height: 22vw;
						overflow: hidden;
						display: flex;
						flex-wrap: wrap;
						position: relative;

						.title {
							width: 100%;
							font-size: 28upx;
							display: -webkit-box;
							-webkit-box-orient: vertical;
							-webkit-line-clamp: 2;
							// text-align: justify;
							overflow: hidden;
						}

						.spec {
							font-size: 20upx;
							background-color: #f3f3f3;
							color: #a7a7a7;
							height: 30upx;
							display: flex;
							align-items: center;
							padding: 0 10upx;
							border-radius: 15upx;
							margin-bottom: 20vw;
						}

						.price-number {
							position: absolute;
							width: 100%;
							bottom: 0upx;
							display: flex;
							justify-content: space-between;
							align-items: flex-end;
							font-size: 28upx;
							height: 60upx;

							.price {
								color: #FF4719;
								font-size: 32upx;
							}

							.number {
								display: flex;
								justify-content: center;
								align-items: flex-end;

								.input {
									width: 60upx;
									height: 60upx;
									margin: 0 10upx;
									background-color: #f3f3f3;

									input {
										width: 60upx;
										height: 60upx;
										display: flex;
										justify-content: center;
										align-items: center;
										text-align: center;
										font-size: 26upx;
									}
								}

								.sub,
								.add {
									width: 60upx;
									height: 60upx;
									background-color: #f3f3f3;
									border-radius: 5upx;
									font-size: 40upx;
									text-align: center;
									line-height: 60upx;
									color: #999999;

									.icon {
										font-size: 22upx;
										width: 45upx;
										height: 45upx;
										display: flex;
										justify-content: center;
										align-items: center;

									}
								}
							}
						}
					}
				}
			}
		}
	}

	.footer {
		width: 92%;
		padding: 0 4%;
		background-color: #FFF;
		height: 100upx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		font-size: 28upx;
		position: fixed;
		bottom: 0upx;
		z-index: 5;

		.delBtn {
			border: solid 1upx #f06c7a;
			color: #f06c7a;
			padding: 0 30upx;
			height: 50upx;
			border-radius: 30upx;
			display: flex;
			justify-content: center;
			align-items: center;
		}

		.settlement {
			width: 60%;
			display: flex;
			justify-content: flex-end;
			align-items: center;

			.sum {
				width: 50%;
				font-size: 28upx;
				margin-right: 10upx;
				display: flex;
				justify-content: flex-end;

				.money {
					font-weight: 500;
					font-size: 30upx;
					color: #FF4719;
				}
			}

			.btn {
				// padding: 0 30upx;
				height: 80upx;
				background: rgba(49, 49, 49, 1);
				color: #fff;
				display: flex;
				justify-content: center;
				align-items: center;
				width: 190upx;
				border-radius: 50upx;
			}
		}
	}
</style>
