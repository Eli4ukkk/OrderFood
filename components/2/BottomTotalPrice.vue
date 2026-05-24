<template>
	<view class="">
		<view class="totalmoneyshadow align-center flex justify-center total-bar">
			<view class="total-bar__content flex justify-center">
				<!-- 总价格 --> 
				<view class="total-price text-bold text-price text-center text-lg">
					{{totalPrice}}
				</view>
				<!-- 确认登记 -->
				<view @click="confirmUp" class="confirm-button text-center text-white text-lg">
					确认登记
				</view>
			</view>
		
			<!-- 购物车图标，绝对定位粘上去 -->
			<view @click="clickCart" class="cart-icon-wrap">
				<image class="cart-icon" src="/static/2/Group 247.png" mode="aspectFit"></image>
				<view
				v-if="cartNumber > 0"
				class="cart-badge flex align-center justify-center">
					{{ cartBadgeText }}
				</view>
			</view>
			
		
		</view>
		
		
		<view v-if="openCart" @click="closeCart" class="cart-mask"></view>
		
		<view v-if="openCart" class="cart-panel flex flex-direction bg-white padding-sm" @click.stop>
			<slot name="slotOne">第一个槽</slot>
		</view>
		
	</view>
</template>

<script>
	export default {
		name: 'BottomTotalPrice',
		props: {
			totalPrice: {
				type: String,
				default: '0.00',
			},
			cart: {
				type: Array,
				default: () => [],
			},
			openCart: {
				type: Boolean,
				default: false,
				required: true,
			}
		},
		data() {
			return {
				
			}
		},
		computed: {
			cartNumber() {
				return this.cart.reduce((total, item) => total + item.number, 0);
			},
			cartBadgeText() {
				if (this.cartNumber > 99) {
					return '99+';
				}
				return this.cartNumber;
			},
			cartStatus() {
				return this.cart.length > 0;
			},
		},
		methods: {
			showToast(message) {
				uni.showToast({
					title: message,
					icon: 'none',
					duration: 1000
				})
			},
			clickCart() {
				
				if (this.cart.length === 0) {
					this.showToast('购物车为空');
				} else {
					if (this.cartStatus) {
						this.$emit('toggleCart', true)
						// console.log(this.openCart);
					}
				}
			},
			confirmUp() {
				if (this.cart.length === 0) {
					this.showToast('请先选择菜品');
				} else {
					// 确认登记逻辑
					
					
				}
			},
			closeCart() {
				this.$emit('toggleCart', false)
				// console.log(this.openCart);
			},
			
		}
	}
</script>

<style>
	.totalmoneyshadow {
		box-shadow: 0 -4rpx 12rpx rgba(0, 0, 0, 0.15);
	}
	.total-bar {
		position: fixed;
		left: 0;
		right: 0;
		bottom: var(--window-bottom, 0px);
		height: 130rpx;
		background-color: #fff;
		z-index: 20;
	}

	.total-bar__content {
		width: calc(100% - 70rpx);
		max-width: 680rpx;
	}

	.total-price {
		flex: 1;
		min-width: 0;
		height: 100rpx;
		line-height: 100rpx;
		color: #FF994B;
		border-radius: 50rpx 0 0 50rpx;
		background-color: #FFE8D0;
	}

	.confirm-button {
		width: 250rpx;
		height: 100rpx;
		line-height: 100rpx;
		border-radius: 0 50rpx 50rpx 0;
		background-color: #FFCB95;
		flex-shrink: 0;
	}

	.cart-icon-wrap {
		position: absolute;
		top: 0rpx;
		left: 45rpx;
		width: 100rpx;
		height: 100rpx;
	}

	.cart-icon {
		width: 100%;
		height: 100%;
		display: block;
	}

	.cart-badge {
		position: absolute;
		top: 0;
		right: 0;
		min-width: 34rpx;
		height: 34rpx;
		padding: 0 8rpx;
		box-sizing: border-box;
		border-radius: 17rpx;
		background-color: #FF9900;
		color: #FFFFFF;
		font-size: 22rpx;
		line-height: 34rpx;
	}

	.cart-mask {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: calc(130rpx + var(--window-bottom, 0px));
		background-color: rgba(0, 0, 0, 0.5);
		z-index: 100;
	}

	.cart-panel {
		position: fixed;
		left: 0;
		right: 0;
		bottom: calc(130rpx + var(--window-bottom, 0px));
		height: 500rpx;
		max-height: 60vh;
		border-radius: 24rpx 24rpx 0 0;
		overflow: hidden;
		z-index: 100050;
		box-sizing: border-box;
	}
</style>
