<template>
	<view 
	style="padding-bottom: 150rpx;"
	class="container">
		
		<TopNav
		title="美尔途订餐"
		:hasBack="true"
		@back="goBack"
		/>
		
		<!-- 订单概况 -->
		<view class="bg-white solid margin-sm padding-sm radius">
			<!-- 订单概况 -->
			<view 
			style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
			class="flex text-black align-center padding-sm justify-center radius">
				订单概况
			</view>
			
			<!-- 订单总数 总金额 -->
			<view class="flex justify-between padding-sm">
				<!-- 订单总数 -->
				<view 
				style="flex: 1; background-color: #FFF6EC; box-shadow: 0 0 5rpx 5rpx rgba(0, 0, 0, 0.1)"
				class="solid radius margin-right padding-sm">
					<view class="text-black ">
						订单总数
					</view>
					<view 
					style="color: #FF992B;"
					class="text-sl text-bold self-end">
						{{ orderSummary.orderCount }}
					</view>
				</view>
				<!-- 总金额 -->
				<view 
				style="flex: 1; background-color: #FFF6EC; box-shadow: 0 0 5rpx 5rpx rgba(0, 0, 0, 0.1)"
				class="solid radius padding-sm">
					<view class="text-black">
						总金额
					</view>
					<view 
					style="color: #FF992B;"
					class="text-sl text-bold text-price">
						{{ orderSummary.totalAmount }}
					</view>
				</view>
			</view>
		</view>
		
		<!-- 订单明细 -->
		<view class="bg-white solid margin-sm padding-sm radius">
			<view
			style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
			class="flex text-black align-center padding-sm justify-center radius">
				订单明细
			</view>
			
			<!-- 去餐号 订单总数 金额 -->
			<view
				class="flex align-center text-black text-bold"
				style="height: 58rpx; padding-left: 8rpx; padding-right: 8rpx; font-size: 30rpx;"
			>
				<view style="width: 118rpx; flex-shrink: 0;">
					<text>取餐号</text>
				</view>
				<view
					class="flex align-center justify-center"
					style="flex: 1; min-width: 0;"
				>
					<text>订餐总数:{{ orderSummary.orderCount }}</text>
				</view>
				<view style="width: 120rpx; flex-shrink: 0; text-align: right;">
					<text>金额</text>
				</view>
			</view>
			
			<!-- 明细 -->
			<view style="padding-top: 12rpx;">
				<view
					v-for="item in orderUserList"
					:key="item.id"
					@tap="handleOrderItemTap(item)"
					class="flex align-center solid-bottom"
					style="min-height: 118rpx; padding-top: 20rpx; padding-bottom: 20rpx;"
				>
					<view
						class="bg-orange light text-orange flex align-center justify-center text-bold"
						style="width: 58rpx; height: 58rpx; border-radius: 50%; margin-left: 4rpx; margin-right: 28rpx; font-size: 30rpx; line-height: 58rpx; flex-shrink: 0;"
					>
						<text>{{ item.pickupCode }}</text>
					</view>
					<view
						style="flex: 1; min-width: 0; padding-right: 16rpx;"
					>
						<view
							class="text-black text-bold text-cut"
							style="font-size: 30rpx; line-height: 40rpx;"
						>
							<text>{{ formatFoodList(item.foodList) }}</text>
						</view>
						<view
							class="text-gray"
							style="font-size: 24rpx; line-height: 34rpx; margin-top: 8rpx;"
						>
							<text>{{ item.phone }}</text>
							<text style="margin-left: 14rpx;">备注:{{ item.remark || '无' }}</text>
						</view>
					</view>
					<view
						class="text-black text-bold text-price"
						style="width: 120rpx; flex-shrink: 0; text-align: right; font-size: 30rpx; line-height: 40rpx;"
					>
						<text>{{ item.amount }}</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 底部导出 -->
		<view 
		style="
		position: fixed;
		z-index: 9;
		border-radius: 20px 20px 0 0;
		bottom: 0; height: 150rpx; width: 100%;"
		class="solid bg-white flex justify-between padding align-center">
			<view class="text-black">
				<view class="">
					总计
				</view>
				<view class="text-xxl text-bl text-price text-bold">
					{{ orderSummary.totalAmount }}
				</view>
			</view>
			<view class="">
				<button
					@tap="handleDownload"
					class="solid cu-btn round text-white"
					style="box-shadow: 0 0 3px 3px rgba(0, 0, 0, 0.1); color: #FF992B; background-color: #FFF6EC; width: 250rpx; height: 90rpx; font-size: 32rpx; line-height: 76rpx;"
				>	
					<view class="cuIcon-down"></view>
					去导出
				</button>
			</view>
		</view>
		
	</view>
</template>

<script>
	import TopNav from "@/components/common/TopNav.vue"
	export default {
		components: {
			TopNav,
		},
		onLoad() {
			this.getOrderPriceData()
		},
		data() {
			return {
				orderSummary: {
					orderCount: 21,
					totalAmount: '321.00',
				},
				orderUserList: [
					{
						id: 1,
						pickupCode: '1',
						phone: '138****8021',
						foodList: [
							{
								name: '啤酒鸭',
								count: 1,
							},
							{
								name: '土豆烧茄子',
								count: 1,
							},
						],
						remark: '少辣',
						amount: '16.00',
					},
					{
						id: 2,
						pickupCode: '2',
						phone: '186****3496',
						foodList: [
							{
								name: '红烧狮子头',
								count: 1,
							},
							{
								name: '千叶豆腐炒肉',
								count: 1,
							},
						],
						remark: '',
						amount: '18.00',
					},
					{
						id: 3,
						pickupCode: '3',
						phone: '159****6178',
						foodList: [
							{
								name: '番茄炒蛋',
								count: 1,
							},
							{
								name: '米饭',
								count: 1,
							},
						],
						remark: '不要葱',
						amount: '14.00',
					},
				]
			}
		},
		methods: {
			getOrderPriceData() {
				// TODO: 后续在这里请求后端，拿到今日 订单概况 和 订单明细
				// this.requestOrderPriceData()
			},
			requestOrderPriceData() {
				// TODO: 后续替换为真实接口
				// return uni.request({
				// 	url: '',
				// 	method: 'GET',
				// })
			},
			formatFoodList(foodList) {
				if (!Array.isArray(foodList)) {
					return ''
				}
				return foodList.map(item => `${item.name}×${item.count}`).join('+')
			},
			handleOrderItemTap(item) {
				// TODO: 后续可在这里打开订单详情或请求单条订单信息
			},
			handleDownload() {
				uni.navigateTo({
					url: '/pages/index/manager/download'
				})
			},
			goBack() {
				uni.navigateBack()
			}
		}
	}
</script>
