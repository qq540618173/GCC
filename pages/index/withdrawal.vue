<template>
	<view class="withdrawal">
		<header-bar :isBg="isBg" :title="i18n.header.header4"></header-bar>
		<view class="container">
			<view class="form-wrap">
				<view class="form-wrap-top">
					<view class="left">{{i18n.withdrawal.lang16}}</view>
					<view class="right">{{indexData.acc?indexData.acc.USDT:'0.0000'}}</view>
				</view>
				<view class="form-wrap-bot">
					<view class="form-item">
						<input type="text" v-model="current.address" :placeholder="i18n.withdrawal.lang17" />
					</view>
					<view class="form-item">
						<input type="digit" v-model="current.amount" :placeholder="i18n.withdrawal.lang18" />
					</view>
					<view class="form-item">
						<label>{{i18n.withdrawal.lang19}}:</label>
						<input class="shrink" type="text" v-model="current.amount?calc:''" placeholder="0.00" disabled="true" />
					</view>
					<view class="form-item">
						<input type="text" v-model="current.paypass" :password="active" :placeholder="i18n.withdrawal.lang20" />
						<view class="password" :class="{'active': !active}" @tap="addClass('active')"></view>
					</view>
					<view class="tip">
						<image src="../../static/images/icon17.png"></image>
						<text>{{i18n.withdrawal.lang21}}</text>
					</view>
				</view>
			</view>
			<view class="withdrawal-submit">
				<view class="form-submit gradient-blue" @tap="submit">{{i18n.withdrawal.lang22}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	import HeaderBar from '../../components/header-bar.vue';
	export default {
		data() {
			return {
				active: true,
				isBg: true,
				current: {       
					address: '',
					amount: '',
					paypass: '',
				},
				rate: '',             //汇率
				indexData: '',        //USDT数量
			}
		},
		components:{
			HeaderBar
		},
		onLoad(){
			this.getRate()
			this.getData()
		},
		methods: {
			addClass(classname){
				this[classname] = !this[classname]
			},
			submit(){
				// 提交信息
				let { current } = this
				this.uniRequest({
					url: 'withdraw',
					data: {
						...current
					}
				}).then(res => {
					uni.showToast({
						title: res.message,
						icon: 'none',
						success: () => {
							setTimeout(()=>{
								this.getData()
								this.current = {
									address: '',
									amount: '',
									paypass: '',
								}
							},2000)
						}
					})
				})
			},
			getRate(){
				// 获取汇率
				this.uniRequest({
					url: 'getRatio',
					method: 'GET'
				}).then(res => {
					this.rate = res.result.withd_ratio
				})
			},
			getData(){
				// 获取USDT
				this.uniRequest({
					url: 'wallet',
					method: 'GET'
				}).then(res => {
					this.indexData = res.result
				})
			}
		},
		computed: {  
			i18n(){  
				return this.$t('index')  
			},
			calc(){
				return (this.current.amount*(1 - (this.rate/100))).toFixed(2)
			}
		}
	}
</script>

<style lang="scss">
.withdrawal{
	.container{
		.form-wrap{
			.form-wrap-bot{
				.tip{
					display: flex;
					align-items: center;
					padding: 10rpx 20rpx 0;
					image{
						width: 32rpx;
						height: 32rpx;
						margin-right: 14rpx;
					}
					text{
						color: #FF4436;
						font-size: $fontJ;
					}
				}
				.shrink{
					min-width: 200rpx;
				}
			}
		}
		.withdrawal-submit{
			margin-top: 40rpx;
		}
	}
}
</style>
