<template>
	<view class="bg-white" style="min-height: 100vh; box-sizing: border-box; padding-bottom: var(--window-bottom, 0px);">
		<TopNav
			title="美尔途订餐"
			:hasBack="false"
		></TopNav>

		<!-- 显示餐 -->
		<view v-if="cartIsHas" style="min-height: 0;">
			<HasOrder
				:orderInfo="orderInfo"
				@cancelOrder="handleCancelOrder"
			></HasOrder>
		</view>

		<!-- 点餐 -->
		<view v-else style="min-height: 0;">
			<GoToOrder @goOrder="handleGoOrder"></GoToOrder>
		</view>

	</view>
</template>

<script>
	/**
	 * @注册组件
	 */
	import GoToOrder from 'components/1/GoToOrder.vue'
	import HasOrder from 'components/1/HasOrder.vue'
	import TopNav from 'components/common/TopNav.vue'
	
	export default {
		/**
		 * @声明组件
		 */
		components: {
			GoToOrder,
			HasOrder,
			TopNav
		},
		data() {
			return {
				cartIsHas: true,
				orderInfo: {
					pickupCode: 47,
					foodList: [
						{
							name: '烧排骨',
							count: 1,
							image: ''
						},
						{
							name: '照烧鸡',
							count: 1,
							image: ''
						}
					],
					remark: '少饭',
					orderTime: '2025-12-24 17:16:27',
					totalAmount: '16.00'
				}
			}
		},
		methods: {
			/**
			 * @组件通信
			 */
			handleGoOrder() {
				uni.switchTab({
					url: '/pages/index/2'
				})
			},
			handleCancelOrder() {
				uni.showModal({
					title: '取消订单',
					content: '确认取消当前订单吗？',
					success: (res) => {
						if (!res.confirm) return

						this.cartIsHas = false
						this.orderInfo = null
					}
				})
			}
		}
	}
</script>
