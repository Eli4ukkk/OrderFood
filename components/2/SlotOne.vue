<template>
		<view class="cart-slot">
			<view class="cart-slot__header flex justify-between solid-bottom padding-bottom-sm">
				<view class="text-bold text-black text-lg">
					已选商品
				</view>
				<view @tap="clearCart" class="text-bold text-black text-lg">
					<image src="/static/2/Vector.png" mode="widthFix" style="width: 20rpx; height: 20rpx;"></image>
					清空
				</view>
			</view>
		
		
		
			<scroll-view scroll-y show-scrollbar class="cart-slot__list">
				<view v-for="(item, index) in cart" :key="index" class="cart-item solid-bottom flex align-center padding-tb-sm">
					<image :src="item.image" mode="aspectFill" class="radius" style="
						width: 130rpx;
						height: 130rpx;
						flex-shrink: 0;
					"></image>
		
					<view class="cart-item__info flex flex-direction justify-between padding-left-sm">
						<text class="text-black  text-cut">{{item.title}}</text>
						<text class="text-price text-black ">{{item.price}}</text>
					</view>
		
					<view class="quantity-actions flex align-center">
						<!-- 减号 -->
						<view @tap="subItem()" v-if="item.number" class="quantity-button quantity-button--outline text-xl">-</view>
		
						<!-- 数量 -->
						<view v-if="item.number" class="quantity-count text-xl text-black">{{item.number}}</view>
		
						<!-- 加号 -->
						<view @tap="addItem('cart')" class="quantity-button quantity-button--solid text-xl text-white">+</view>
					</view>
				</view>
		
				<!-- 备注 -->
				<view v-if="cartIsNotEmpty" class="flex justify-between" style="height: 100rpx;">
					<view class="text-black text-xl text-bold" style="line-height: 100rpx;">
						备注
					</view>
					<view @tap="openRemark = true" class="" style="line-height: 100rpx;">
						{{ remark ? remark : '可向商家提供菜品需求' }}
						<text class="cuIcon-right"></text>
					</view>
				</view>
		
				<view v-else class="text-bold text-black text-xxl text-center margin-top-sm">
					暂无添加商品
				</view>
		
			</scroll-view>
		
			<!-- 模态框 -->
			<view
				v-if="modelAppear"
				class="cu-modal show"
				@tap="cancelClear"
				style="z-index: 100200;"
			>
				<view class="cu-dialog" @tap.stop>
					<view class="cu-bar bg-white justify-center">
						<view class="content">确认清空</view>
					</view>
					<view class="padding-xl bg-white">
						确定要清空购物车吗？
					</view>
					<view class="cu-bar bg-white flex justify-center">
						<view class="action">
							<button class="cu-btn line-green text-green" @tap="cancelClear">取消</button>
							<button class="cu-btn bg-green margin-left" @tap="confirmClear">确认</button>
						</view>
					</view>
				</view>
			</view>
		
			<!-- 备注页面 -->
			<view v-if="openRemark" class="">
				<view class="remark-panel flex flex-direction bg-white padding-sm" @tap.stop>
					<view style="height: 70rpx;" class="flex justify-between solid-bottom padding-bottom-sm">
						<view @tap="cancelRemark" style="line-height: 70rpx;"
							class="cuIcon-back text-bold text-black">
						</view>
						<view class="text-bold text-black text-lg">
							添加备注
						</view>
						<view @tap="confirmRemark" style="line-height: 70rpx;" class="text-bold text-black">
							完成
						</view>
		
					</view>
		
		
					<textarea
						:value="remarkValue"
						@input="changeRemark"
						placeholder="请输入口味，偏好等要求"
						maxlength="140"
						style="width: 100%; height: 260rpx; border: 1rpx solid #dddddd; border-radius: 8rpx; padding: 20rpx; box-sizing: border-box;"
					></textarea>
					<view class="text-right text-grey text-sm margin-top-xs">
						{{ remarkValue.length }}/140
					</view>
				</view>
			</view>
		
			<!-- 提示备注保成功 -->
			<view
				v-if="successSaveRemark"
				@tap="successSaveRemark = false"
				style="position: fixed; left: 0; right: 0; top: 0; bottom: 0; z-index: 100300; background-color: rgba(0, 0, 0, 0.25);"
			>
				<view class="warp">
					<view class="rect text-black text-center">
						备注保存成功
					</view>
				</view>
			</view>
		
	</view>
</template>

<script>
	export default {
		name: 'SlotOne',
		props: {
			cart: {
				type: Array,
				default: () => [],
			},
			remark: {
				type: String,
				default: '',
			}
		},
		data() {
			return {
				modelAppear: false,
				openRemark: false,
				remarkValue: '', // 确定的备注内容
				remarkValue_: '', // 记录上一次的备注内容，方便取消时恢复
				successSaveRemark: false, // 成功保存备注的提示
			};
		},
		computed: {
			cartIsNotEmpty() {
				return this.cart.length > 0;
			}
		},
		methods: {
			showToast(message) {
				uni.showToast({
					title: message,
					icon: 'none',
					duration: 1000
				})
			},
			
			closeCart() {
				this.openCart = false;
			},
			
			clearCart() {
				this.modelAppear = true
			},
			
			cancelClear() {
				this.modelAppear = false;
			},
			
			confirmClear() {
				this.$emit('clearCart', []);
				
				this.modelAppear = false;
				this.openCart = false;
				this.showToast('已清空购物车');
			},
			
			cancelRemark() {
				this.openRemark = false;
				this.remarkValue = this.remarkValue_;
			},
			
			confirmRemark() {
				this.successSaveRemark = true; // 保存成功提示
				this.openRemark = false;
				this.remarkValue_ = this.remarkValue; // 更新上一次的备注内容
				
				this.$emit('updateRemarkValue', this.remarkValue) 	// 显示在备注位置
			},
			
			changeRemark(e) {
				this.remarkValue = e.detail.value;
			},
		},
	}
</script>

<style>
	.cart-slot {
		height: 100%;
		display: flex;
		flex-direction: column;
		min-height: 0;
	}

	.cart-slot__header {
		flex-shrink: 0;
	}

	.cart-slot__list {
		flex: 1;
		min-height: 400rpx;
	}

	.cart-item {
		width: 100%;
		box-sizing: border-box;
	}

	.cart-item__info {
		flex: 1;
		height: 130rpx;
		min-width: 0;
		overflow: hidden;
	}

	.quantity-actions {
		margin-left: auto;
		flex-shrink: 0;
		align-self: flex-end;
	}

	.quantity-button,
	.quantity-count {
		width: 50rpx;
		height: 50rpx;
		line-height: 50rpx;
		text-align: center;
		border-radius: 50%;
		box-sizing: border-box;
		flex-shrink: 0;
	}

	.quantity-button--outline {
		color: #5D9A7D;
		border: 1px solid #5D9A7D;
	}

	.quantity-button--solid {
		background-color: #5D9A7D;
		border: 1px solid #5D9A7D;
	}

	.remark-panel {
		position: fixed;
		left: 0;
		right: 0;
		bottom: calc(130rpx + var(--window-bottom, 0px));
		height: 700rpx;
		max-height: 72vh;
		border-radius: 24rpx 24rpx 0 0;
		overflow: hidden;
		z-index: 100050;
		box-sizing: border-box;
	}

	.warp {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 100%;
	}
	
	/* 弹窗 */
	.rect {
		width: 200rpx;
		height: 100rpx;
		border-radius: 20rpx;
		background-color: greenyellow;
		line-height: 100rpx;
	}
</style>
