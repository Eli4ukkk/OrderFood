<template>
	<view 
	style=""
	class=" container">
		<TopNav
		title="美尔途订餐"
		:hasBack="true"
		@back="goBack"
		/>
		
		<view 
		style="box-shadow: 0 0 5rpx 5rpx rgba(0, 0, 0, 0.2);"
		class="solid bg-white margin-sm radius padding-sm">
			<!-- 未订餐 已订餐 -->
			<view
				class="flex align-center text-black"
				style="position: relative; width: 100%; height: 70rpx; flex-shrink: 0;"
			>
				<view
					v-if="selectLeftRight"
					@tap="selectTab(true)"
					class="flex align-center text-bold"
					style="position: relative; z-index: 1; width: 126rpx; height: 70rpx; margin-right: 22rpx; flex-shrink: 0; line-height: 70rpx; white-space: nowrap; font-size: 38rpx;"
				>
					<text>未订餐</text>
				</view>
				<view
					v-else
					@tap="selectTab(true)"
					class="flex align-center"
					style="position: relative; z-index: 1; width: 126rpx; height: 70rpx; margin-right: 22rpx; flex-shrink: 0; line-height: 70rpx; white-space: nowrap; font-size: 34rpx;"
				>
					<text>未订餐</text>
				</view>
				<view
					v-if="!selectLeftRight"
					@tap="selectTab(false)"
					class="flex align-center text-bold"
					style="position: relative; z-index: 1; width: 126rpx; height: 70rpx; margin-right: 22rpx; flex-shrink: 0; line-height: 70rpx; white-space: nowrap; font-size: 38rpx;"
				>
					<text>已订餐</text>
				</view>
				<view
					v-else
					@tap="selectTab(false)"
					class="flex align-center"
					style="position: relative; z-index: 1; width: 126rpx; height: 70rpx; margin-right: 22rpx; flex-shrink: 0; line-height: 70rpx; white-space: nowrap; font-size: 34rpx;"
				>
					<text>已订餐</text>
				</view>
				<view
					:animation="tabLineAnimation"
					style="position: absolute; left: 38rpx; bottom: 4rpx; width: 28rpx; height: 6rpx; background-color: #f37b1d; border-radius: 999rpx;"
				></view>
			</view>

			<!-- 未订餐内容 -->
			<view
				v-if="selectLeftRight"
				style="padding-top: 36rpx;"
			>
				<view
					class="flex align-center justify-between text-black text-bold"
					style="height: 58rpx; padding-left: 8rpx; padding-right: 8rpx; font-size: 32rpx;"
				>
					<text>序号</text>
					<text>人数:{{ unorderUserList.length }}</text>
				</view>

				<view style="padding-top: 12rpx;">
					<view
						v-for="(item, index) in unorderUserList"
						:key="item.id"
						class="flex align-center solid-bottom"
						style="height: 92rpx;"
					>
						<view
							class="bg-orange text-white flex align-center justify-center text-bold"
							style="width: 42rpx; height: 42rpx; border-radius: 50%; margin-left: 6rpx; margin-right: 34rpx; font-size: 26rpx; line-height: 42rpx; flex-shrink: 0;"
						>
							<text>{{ index + 1 }}</text>
						</view>
						<view
							class="text-black"
							style="font-size: 32rpx; line-height: 92rpx;"
						>
							<text>{{ item.phone }}</text>
						</view>
					</view>
				</view>

				<view
					class="flex align-center justify-center"
					style="padding-top: 36rpx; padding-bottom: 8rpx;"
				>
					<button
						@tap="handleRemindAll"
						class="cu-btn round text-white"
						style="background-color: #559862; width: 360rpx; height: 76rpx; font-size: 32rpx; line-height: 76rpx;"
					>
						一键提醒
					</button>
				</view>
			</view>

			<view
				v-else
				style="padding-top: 36rpx;"
			>
				<!-- 已订餐内容 -->
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
						<text>订餐总数:{{ orderUserList.length }}</text>
					</view>
					<view style="width: 120rpx; flex-shrink: 0; text-align: right;">
						<text>金额</text>
					</view>
				</view>

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
			
		</view>
		<!-- box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.2); -->
		
		
	</view>
</template>

<script>
	/**
	 * @注册组件
	 */
	import TopNav from "@/components/common/TopNav.vue"
	
	export default {
		/**
		 * @声明组件
		 */
		components: {
			TopNav,
		},
		data() {
			return {
				selectLeftRight: true, // true为左边 false为右边
				tabLineAnimation: {},
				unorderUserList: [
					{
						id: 1,
						phone: '138****8021',
					},
					{
						id: 2,
						phone: '186****3496',
					},
					{
						id: 3,
						phone: '159****6178',
					},
				],
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
				],
			}
		},
		methods: {
			selectTab(isUnorder) {
				if (this.selectLeftRight === isUnorder) {
					return
				}
				this.selectLeftRight = isUnorder
				this.moveTabLine(isUnorder)
			},
			moveTabLine(isUnorder) {
				const animation = uni.createAnimation({
					duration: 220,
					timingFunction: 'ease',
				})
				animation.translateX(isUnorder ? 0 : uni.upx2px(148)).step()
				this.tabLineAnimation = animation.export()
			},
			handleRemindAll() {
				// TODO: 对接后端后一键提醒未订餐用户
			},
			formatFoodList(foodList) {
				if (!Array.isArray(foodList)) {
					return ''
				}
				return foodList.map(item => `${item.name}×${item.count}`).join('+')
			},
			handleOrderItemTap(item) {
				// TODO: 对接后端后处理已订餐用户订单详情
			},
			goBack() {
				uni.navigateBack()
			}
		}
	}
</script>
