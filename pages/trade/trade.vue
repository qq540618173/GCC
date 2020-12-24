<template>
	<view class="trade">
		<header-bar :isBack="isBack" :isBg="isBg" :title="i18n.header.header29"></header-bar>
		<view class="content">
			<view class="charts-wrap">
				<view class="charts-title">
					<text>{{i18n.trade.trade1}}</text>
				</view>
				<canvas canvas-id="canvasLineA" id="canvasLineA" class="charts" @touchstart="touchLineA"></canvas>
				<view class="bot-data">
					<text>{{i18n.trade.trade6}}：</text>
					<text>{{botData}}$</text>
				</view>
			</view>
			<view class="charts-wrap mt24">
				<view class="charts-title">
					<text>{{i18n.trade.trade2}}</text>
				</view>
				<view class="table">
					<view class="th">
						<view>{{i18n.trade.trade3}}</view>
						<view>{{i18n.trade.trade4}}</view>
						<view>{{i18n.trade.trade5}}</view>
					</view>
					<view class="tbody">
						<view class="td" v-for="(item, index) in marketInfo" :key="index">
							<view><image :src="item.logo"></image>{{item.pair}}</view>
							<view>${{item.price}}</view>
							<view>
								<text :class="[item.percent_change_display >= 0?'increase':'reduce']">{{item.percent_change_display}}%</text>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="app">
				<view class="title2 title-line"></view>
				<view class="app-item">
					<view class="img"><image src="../../static/images/icon79.png"></image></view>
					<view class="info">
						<view class="name">
							<text>OTC交易</text>
						</view>
						<view class="details">
							<text>支持USDT、ETH、BTC等</text>
						</view>
					</view>
				</view>
				<view class="app-item">
					<view class="img"><image src="../../static/images/icon72.png"></image></view>
					<view class="info">
						<view class="name">
							<text>Uniswap</text>
						</view>
						<view class="details">
							<text>去中心化交易所</text>
						</view>
					</view>
				</view>
				<view class="app-item">
					<view class="img"><image src="../../static/images/icon80.png"></image></view>
					<view class="info">
						<view class="name">
							<text>SushiSwap</text>
						</view>
						<view class="details">
							<text>去中心化交易所</text>
						</view>
					</view>
				</view>
				<view class="app-item">
					<view class="img"><image src="../../static/images/icon89.jpg"></image></view>
					<view class="info">
						<view class="name">
							<text>Fitness shopping mall</text>
						</view>
						<view class="details">
							<text>健身购物商城</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<tabbar :isCurrent="3"></tabbar>
	</view>
</template>

<script>
	import HeaderBar from '../../components/header-bar.vue'
	import Tabbar from '../../components/tabbar.vue'
	import uCharts from '../../components/u-charts.min'
	
	var _self;
	var canvaLineA=null;
	export default {
		data() {
			return {
				isBack: false,
				isBg: true,
				cWidth:'',
				cHeight:'',
				pixelRatio:1,
				chartData: {
					categories: [],
					series: []
				},
				marketInfo: [],
				botData: '暂无数据',
			}
		},
		components: {
			HeaderBar,
			Tabbar
		},
		onLoad() {
			_self = this;
			this.cWidth=uni.upx2px(690);
			this.cHeight=uni.upx2px(500);
			this.getData()
			this.getMarketInfo()
		},
		methods: {
			showLineA(canvasId,chartData){
				canvaLineA=new uCharts({
					$this:_self,
					canvasId: canvasId,
					type: 'line',
					fontSize:11,
					legend:{
						show:true,
						fontColor: '#ffffff',
						fontSize: 14
					},
					dataLabel:false,
					dataPointShape:true,
					background:'#FFFFFF',
					pixelRatio:1,
					categories: chartData.categories,
					series: chartData.series,
					animation: true,
					xAxis: {
						disableGrid: true
					},
					yAxis: {
						gridType: 'dash',
						gridColor: '#ffffff',
						dashLength: 10,
						splitNumber: 2,
					},
					width: this.cWidth,
					height: this.cHeight,
					extra: {
						line:{
							type: 'straight'
						}
					}
				});
			},
			touchLineA(e) {
				canvaLineA.showToolTip(e, {
					format: function (item, category) {
						return category + ' ' + item.name + ':' + item.data 
					}
				});
			},
			getData(){
				this.uniRequest({
					url: 'getPriceLog',
					method: 'GET',
					data: {
						limit: 6
					}
				}).then(res => {
					this.chartData = {
						categories: res.result.list.create_time,
						series: [
							{
								name: 'GCC价格(usdt)',
								data:  res.result.list.price,
							}
						]
					}
					this.showLineA("canvasLineA", this.chartData)
					this.botData = res.result.list.maker[0]?res.result.list.maker[0]:''
				})
			},
			getMarketInfo(){
				this.uniRequest({
					url: 'marketInfo',
					method: 'GET',
				}).then(res => {
					this.marketInfo = res.result
				})
			}
		},
		computed: {  
			i18n () {  
				return this.$t('index')  
			}  
		}
	}
</script>

<style lang="scss">
/* #ifdef H5 */
uni-page-body{
	padding-bottom: 100rpx;
}
/* #endif */
.trade{
	.content{
		background-size: contain;
		.bot-data{
			background-color: #34374D;
			border-radius: 0 0 8rpx 8rpx;
			padding: 0 30rpx 40rpx;
			text{
				color: #FFFFFF;
				line-height: 50rpx;
			}
		}
		.app{
			margin-top: 72rpx;
			padding: 0 30rpx 20rpx;
			.title{
				width: 194rpx;
				height: 42rpx;
				background: url(../../static/images/icon70.png) no-repeat;
				background-size: contain;
			}
			.title1{
				width: 90rpx;
				height: 42rpx;
				background: url(../../static/images/icon74.png) no-repeat;
				background-size: contain;
			}
			.title2{
				width: 78rpx;
				height: 42rpx;
				background: url(../../static/images/icon78.png) no-repeat;
				background-size: contain;
			}
			.title3{
				width: 82rpx;
				height: 42rpx;
				background: url(../../static/images/icon81.png) no-repeat;
				background-size: contain;
			}
			.title-line{
				position: relative;
				margin-bottom: 72rpx;
				&::after{
					content: "";
					display: block;
					width: 70rpx;
					height: 6rpx;
					background-color: #59D0CF;
					position: absolute;
					left: 50%;
					margin-left: -35rpx;
					bottom: -10rpx;
				}
			}
			.app-item{
				display: flex;
				align-items: center;
				margin-bottom: 52rpx;
				position: relative;
				&::after{
					content: "暂未开放";
					display: flex;
					justify-content: flex-end;
					align-items: flex-start;
					color: #999999;
					font-size: 24rpx;
					padding: 30rpx;
					position: absolute;
					top: 0;
					left: 0;
					right: 0;
					bottom: 0;
					background: rgba(0, 0, 0, 0.25);
				}
				.img{
					width: 112rpx;
					height: 112rpx;
					margin-right: 40rpx;
					image{
						width: 100%;
						height: 100%;
					}
				}
				.name{
					font-size: 36rpx;
					color: #FFFFFF;
					margin-bottom: 4rpx;
				}
				.details{
					font-size: 28rpx;
					color: #999999;
				}
			}
		}
		.swiper1{
			margin: 40rpx 30rpx 30rpx;
			image{
				width: 100%;
				height: 100%;
			}
		}
		.mt24{
			margin-top: 24rpx;
		}
		.table{
			background-color: #34374D;
			border-radius: 0 0 8rpx 8rpx;
			padding: 20rpx 30rpx 40rpx;
			.th{
				display: flex;
				justify-content: center;
				align-items: center;
				view{
					flex: 1;
					font-size: 28rpx;
					color: #FFFFFF;
					text-align: center;
					line-height: 100rpx;
					background-color: #2f3247;
				}
			}
			.td{
				display: flex;
				justify-content: center;
				align-items: center;
				view{
					flex: 1;
					font-size: 28rpx;
					color: #FFFFFF;
					text-align: center;
					line-height: 100rpx;
					height: 100rpx;
					display: flex;
					align-items: center;
					justify-content: center;
					&:first-child{
						justify-content: left;
						padding-left: 10rpx;
					}
					text{
						display: inline-block;
						width: 150rpx;
						height: 50rpx;
						line-height: 50rpx;
						&.increase{
							background-color: #33D38A;
						}
						&.reduce{
							background-color: #ff0000;
						}
					}
					image{
						width: 60rpx;
						height: 60rpx;
						margin-right: 6rpx;
					}
				}
				&:nth-child(even){
					view{
						background-color: #2f3247;
					}
				}
				&:nth-child(odd){
					view{
						background-color: #272a3f;
					}
				}
			}
		}
		.charts-wrap{
			padding: 0 30rpx;
			overflow: hidden;
			position: relative;
			.charts-title{
				background-color: #34374D;
				padding: 20rpx 0;
				border-radius: 8rpx 8rpx 0 0;
				font-size: 36rpx;
				color: #47E0FB;
				text-align: center;
			}
			.qiun-charts {
				width: 690upx;
				height: 500upx;
				background-color: #34374D;
			}
			
			.charts {
				width: 690upx;
				height: 500upx;
				background-color: #34374D;
			}
		}
		.area{
			padding: 32rpx 30rpx 70rpx;
			margin: 24rpx 30rpx 0;
			background-color: #34374D;
			border-radius: 8rpx;
			.area-title{
				width: 314rpx;
				height: 38rpx;
				background: url('../../static/images/icon51.png') no-repeat;
				background-size: contain;
				margin: 0 auto;
			}
			.area-content{
				margin-top: 38rpx;
				position: relative;
				image{
					width: 100%;
				}
			}
		}
		.game{
			padding: 32rpx 30rpx 70rpx;
			margin: 24rpx 30rpx 0;
			background-color: #34374D;
			border-radius: 8rpx;
			.game-title{
				width: 228rpx;
				height: 38rpx;
				background: url('../../static/images/icon55.png') no-repeat;
				background-size: contain;
				margin: 0 auto;
			}
			.game-content{
				margin-top: 38rpx;
				position: relative;
				image{
					width: 100%;
				}
			}
		}
		.none{
			position: absolute;
			left: 50%;
			top: 50%;
			transform: translate(-50%, -50%);
			font-size: $fontH;
			color: $colorE;
		}
	}
}
</style>
