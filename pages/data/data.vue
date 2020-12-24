<template>
	<view class="data">
		<header-bar :isBack="isBack" isBg="isBg" :title="i18n.tabbar.data"></header-bar>
		<view class="content">
			<view class="swiper-title">
				<text>{{i18n.data.d1}}</text>
			</view>
			<swiper class="swiper-con" :indicator-dots="true" :autoplay="true">
				<swiper-item>
					<view class="swiper-item bg2">
						<view class="swiper-top">
							<image src="../../static/images/icon20.png"></image>
							<text>{{i18n.data.lang32}}</text>
						</view>
						<view class="swiper-bot">
							<text class="big">{{dataList.pool?dataList.pool.total:0.00}}</text>
							<text class="small">GCC</text>
						</view>
					</view>
				</swiper-item>
				<swiper-item>
					<view class="swiper-item bg1">
						<view class="swiper-top">
							<image src="../../static/images/icon21.png"></image>
							<text>{{i18n.data.lang33}}</text>
						</view>
						<view class="swiper-bot">
							<text class="big">{{dataList.pool?dataList.pool.market:0.00}}</text>
							<text class="small">GCC</text>
						</view>
					</view>
				</swiper-item>
				<swiper-item>
					<view class="swiper-item bg3">
						<view class="swiper-top">
							<image src="../../static/images/icon22.png"></image>
							<text>{{i18n.data.lang34}}</text>
						</view>
						<view class="swiper-bot">
							<text class="big">{{dataList.pool?dataList.pool.released:0.00}}</text>
						</view>
					</view>
				</swiper-item>
			</swiper>
			<view class="swiper-title mt60">
				<text>{{i18n.data.d2}}</text>
			</view>
			<view class="count-wrap">
				<view class="box-wrap">
					<view class="recommend">
						<view class="recommend-item">
							<view class="top">
								<text>{{i18n.data.d3}}</text>
							</view>
							<view class="bot">
								<text>{{dataList.community?dataList.community.recom:0.00}}</text>
							</view>
						</view>
						<view class="recommend-item">
							<view class="top">
								<text>{{i18n.data.d4}}</text>
							</view>
							<view class="bot">
								<text>{{dataList.community?dataList.community.award:0.00}}</text>
							</view>
						</view>
					</view>
					<view class="hr"></view>
					<swiper class="swiper-team" :indicator-dots="true" :autoplay="true">
						<swiper-item class="swiper-item">
							<view class="title">
								<text>{{i18n.data.d5}}:</text>
							</view>
							<view class="con">
								<text class="big">{{dataList.team?dataList.team.poolTotal:0.00}}</text>
								<text class="small">USDT</text>
							</view>
						</swiper-item>
						<swiper-item class="swiper-item">
							<view class="title">
								<text>{{i18n.data.d6}}:</text>
							</view>
							<view class="con">
								<text class="big">{{dataList.team?dataList.team.last_month:0.00}}</text>
								<text class="small">USDT</text>
							</view>
						</swiper-item>
						<swiper-item class="swiper-item">
							<view class="title">
								<text>{{i18n.data.d7}}:</text>
							</view>
							<view class="con">
								<text class="big">{{dataList.team?dataList.team.addPool:0.00}}</text>
								<text class="small">USDT</text>
							</view>
						</swiper-item>
					</swiper>
					<view class="reward">
						<view class="reward-item month" :class="{active: dataList.award&&dataList.award.is_month == 1}" @tap="getPrize('getMonthPrize')">
							<text>{{i18n.data.d8}}</text>
						</view>
						<view class="reward-item quarter" :class="{active: dataList.award&&dataList.award.is_quarer == 1}" @tap="getPrize('getQuartePrize')">
							<text>{{i18n.data.d9}}</text>
						</view>
					</view>
				</view>
			</view>
			<view class="adv"></view>
		</view>
		<tabbar :isCurrent="2"></tabbar>
	</view>
</template>

<script>
	import HeaderBar from '../../components/header-bar.vue'
	import Tabbar from '../../components/tabbar.vue'
	export default {
		data() {
			return {
				isBack: false,
				isBg: true,
				dataList: [],
				slideList: [],
				current: 1,
				teamsMill: [],
			}
		},
		components: {
			HeaderBar,
			Tabbar
		},
		onLoad() {
			this.getData()
			// this.getSlideData()
		},
		methods: {
			getData(){
				this.uniRequest({
					url: 'performance',
					method: 'GET'
				}).then(res => {
					this.dataList = res.result
				})
				// this.uniRequest({
				// 	url: 'teamsMill'
				// }).then(res => {
				// 	this.teamsMill = res.result
				// })
			},
			getSlideData(){
				this.uniRequest({
					url: 'slideshow',
					method: 'GET'
				}).then(res => {
					this.slideList = res.result
				})
			},
			getPrize(url){
				// 获取月奖励
				this.uniRequest({
					url,
					method: 'GET'
				}).then(res => {
					uni.showToast({
						title: res.message,
						icon: 'none',
						success: () => {
							setTimeout(() => {
								this.getData()
							}, 2000)
						}
					})
				})
			},
			receive(status){
				//领取超级节点
				if(status>-1) return false
				this.uniRequest({
					url: 'getSuperNode',
					method: 'GET'
				}).then(res => {
					uni.showToast({
						title: res.message,
						icon: 'none',
						success: () => {
							setTimeout(() => {
								this.getData()
							}, 2000)
						}
					})
				})
			},
			changeTab(status){
				this.current = status
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
.data{
	min-height: 100%;
	.content {
		width: 100%;
		min-height: 100%;
		box-sizing: border-box;
		padding: 0 30rpx 30rpx;
		background-size: contain;
		.swiper{
			margin-top: 40rpx;
			image{
				width: 100%;
				height: 100%;
			}
		}
		.swiper-title{
			font-size: 48rpx;
			color: #FFFFFF;
		}
		.swiper-con{
			margin-top: 28rpx;
			.swiper-item{
				height: 300rpx;
				padding: 30rpx 30rpx 0;
				border-radius: 16rpx;
				box-sizing: border-box;
				&.bg1{
					background: url(../../static/images/swiper.png) no-repeat;
					background-size: cover;
				}
				&.bg2{
					background: url(../../static/images/swiper2.png) no-repeat;
					background-size: cover;
				}
				&.bg3{
					background: url(../../static/images/swiper3.png) no-repeat;
					background-size: cover;
				}
				.swiper-top{
					font-size: 36rpx;
					color: #FFFFFF;
					display: flex;
					align-items: center;
					image{
						width: 48rpx;
						height: 48rpx;
						margin-right: 10rpx;
					}
				}
				.swiper-bot{
					color: #FFFFFF;
					text-align: center;
					margin-top: 50rpx;
					.big{
						font-size: 64rpx;
					}
					.small{
						font-size: 42rpx;
					}
				}
			}
		}
		.mt60{
			margin-top: 60rpx;
		}
		.hr{
			margin: 50rpx 0;
			height: 0;
			border-bottom: 1px dashed #4B5166;
		}
		.count-wrap{
			padding: 60rpx 0 44rpx;
			margin-top: 28rpx;
			border-radius: 8rpx;
			background: linear-gradient(180deg, #292F3C 0%, #000000 100%);
			box-shadow: 0px 4px 4px 0px rgba(0, 0, 0, 0.05);
			.box-wrap{
				padding: 0 30rpx;
				.recommend{
					display: flex;
					justify-content: space-between;
					align-items: center;
					.recommend-item{
						width: 300rpx;
						padding: 30rpx 0;
						border-radius: 8rpx;
						text-align: center;
						&:first-child{
							background: linear-gradient(135deg, #A59BFB 0%, #6C62F5 100%);
						}
						&:last-child{
							background: linear-gradient(135deg, #FED2E1 0%, #FCA6BF 100%);
						}
						.top{
							font-size: 24rpx;
							color: #FFFFFF;
						}
						.bot{
							font-size: 36rpx;
							color: #FFFFFF;
							margin-top: 4rpx;
						}
					}
				}
			}
		}
		.swiper-team{
			background-color: #1C1F2A;
			border-radius: 8rpx;
			height: 260rpx;
			padding: 40rpx 40rpx 20rpx;
			box-sizing: border-box;
			.title{
				font-size: 28rpx;
				color: #FFFFFF;
			}
			.con{
				color: #47E0FB;
				text-align: center;
				margin-top: 20rpx;
				.big{
					font-size: 56rpx;
				}
				.small{
					font-size: 28rpx;
				}
			}
		}
		.reward{
			margin-top: 36rpx;
			display: flex;
			justify-content: space-between;
			align-items: center;
			.reward-item{
				width: 290rpx;
				height: 96rpx;
				line-height: 96rpx;
				text-align: center;
				background-color: #1C1F2A;
				border-radius: 8rpx;
				&.month{
					color: #47E0FB;
				}
				&.quarter{
					color: #FCA6BF;
				}
				&.active{
					position: relative;
					&::after{
						content: "";
						display: block;
						width: 12rpx;
						height: 12rpx;
						background-color: #FF4436;
						border-radius: 50%;
						position: absolute;
						right: 70rpx;
						top: 30rpx;
					}
				}
			}
		}
		.adv{
			height: 624rpx;
			background: url(../../static/images/adv.png) center no-repeat;
			background-size: cover;
		}
	}
}
</style>
