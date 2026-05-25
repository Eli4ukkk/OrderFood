<template>
	<view
		class="bg-white flex flex-direction align-center"
		style="height: calc(100vh - 88rpx - var(--window-bottom, 0px)); overflow: hidden; box-sizing: border-box; padding: 12rpx 30rpx 24rpx;"
	>
		<view class="flex align-center justify-center" style="width: 100%; flex: 1 1 auto; min-height: 0;">
			<view style="position: relative; width: 600rpx; max-width: 82vw; height: 100%; max-height: 600rpx;">
				<image
					src="/static/1/Group 284.png"
					mode="aspectFit"
					style="position: absolute; left: 0; top: 0; width: 100%; height: 100%;"
				></image>
				<view
					class="flex align-center justify-center text-lg text-bold"
					style="position: absolute; left: 50%; top: 70.5%; transform: translate(-50%, -50%); width: 230rpx; height: 227rpx; margin-left: -1rpx;"
				>
					<text>暂无取餐码</text>
				</view>
			</view>
		</view>

		<view class="text-black text-lg text-bold" style="flex: 0 0 auto; line-height: 44rpx;">快去选美味的餐品吧！</view>

		<button
			class="cu-btn round margin-top-lg text-white text-xl text-bold"
			style="background-color: #447E4F; width: 200rpx; height: 80rpx; flex: 0 0 auto;"
			@click="handleGoOrder"
		>
			去点餐
		</button>

		<image
			src="/static/1/Group 308.png"
			mode="aspectFit"
			style="width: 690rpx; max-width: 92vw; height: 26vh; max-height: 330rpx; margin-top: 20rpx; flex: 0 0 auto;"
		></image>
	</view>
</template>

<script>
	export default {
		name: 'GoToOrder',
		methods: {
			handleGoOrder() {
				this.$emit('goOrder')
			}
		}
	}
</script>
