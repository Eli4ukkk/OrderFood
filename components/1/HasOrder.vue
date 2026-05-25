<template>
	<view
		class="bg-white flex flex-direction margin-top-xl"
		style="position: relative; height: calc(100vh - 88rpx - var(--window-bottom, 0px)); box-sizing: border-box; padding-bottom: 24rpx;"
	>
		<view 
		class="flex justify-center"
		style="z-index: 3; position: relative; height: 320rpx; flex: 0 0 auto;">
			<image
				src="/static/1/Group 284.png"
				mode="aspectFill"
				style="width: 50%; height: 320rpx; transform: scale(1.75);"
			></image>
		</view>

		<view
			class="flex flex-direction align-center justify-center"
			style="position: absolute; left: 50%; top: 160rpx; z-index: 5; width: 265rpx; height: 265rpx; margin-left: -135rpx;"
		>
			<view class="text-orange text-bold" style="font-size: 86rpx; line-height: 96rpx;">
				{{ safeOrderInfo.pickupCode }}
			</view>
			<view class="text-orange text-xl text-bold margin-top-xs">取餐码</view>
		</view>

		<view
			class="bg-white radius padding-lr-lg padding-bottom-lg"
			style="box-shadow: 0 0 5rpx 5rpx rgba(0, 0, 0, 0.2); position: relative; z-index: 2; margin: -30rpx 24rpx 0; padding-top: 140rpx; flex: 0 0 auto;"
		>
			<view class="text-black text-xl text-bold">订单明细</view>
			<FoodInfo
				mode="currentOrder"
				:showCheck="false"
				:foodHistory="foodInfoList"
			></FoodInfo>
		</view>

		<view class="margin-lr-xl" style="margin-top: 36rpx; flex: 0 0 auto;">
			<view class="text-black text-xl text-bold">订单信息</view>
			<view class="flex align-center justify-between margin-top text-gray text-df">
				<text>订单金额</text>
				<text>¥{{ safeOrderInfo.totalAmount }}</text>
			</view>
		</view>

		<view class="flex justify-end margin-lr-xl" style="margin-top: 24rpx; flex: 0 0 auto;">
			<button
				class="cu-btn round bg-orange light text-df"
				style="width: 208rpx; height: 68rpx;"
				@click="handleCancel"
			>
				取消订单
			</button>
		</view>
	</view>
</template>

<script>
	import FoodInfo from 'components/common/FoodInfo.vue'

	const defaultOrderInfo = {
		pickupCode: '',
		foodList: [],
		remark: '',
		orderTime: '',
		totalAmount: '0.00'
	}

	export default {
		name: 'HasOrder',
		components: {
			FoodInfo
		},
		props: {
			orderInfo: {
				type: Object,
				default: () => ({ ...defaultOrderInfo })
			}
		},
		computed: {
			safeOrderInfo() {
				return {
					...defaultOrderInfo,
					...(this.orderInfo || {})
				}
			},
			foodInfoList() {
				const foodList = this.safeOrderInfo.foodList || []
				const firstFoodWithImage = foodList.find(item => item.image)

				return [
					{
						url_image: firstFoodWithImage ? firstFoodWithImage.image : '',
						list: foodList,
						remark: this.safeOrderInfo.remark,
						time: this.safeOrderInfo.orderTime,
						price: this.safeOrderInfo.totalAmount
					}
				]
			}
		},
		methods: {
			handleCancel() {
				this.$emit('cancelOrder')
			}
		}
	}
</script>
