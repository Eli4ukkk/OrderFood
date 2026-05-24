<template>
	<view class="bg-white" style="width: 100%; box-sizing: border-box;">
		<!-- 状态栏占位，适配刘海屏 / 状态栏高度 -->
		<view :style="{ height: statusBarHeight + 'px' }"></view>

		<!-- 导航栏主体 -->
		<view
			class="flex align-center"
			:style="{ position: 'relative', width: '100%', height: navBarHeight + 'px' }"
		>

			<!-- 左侧返回按钮 -->
			<view
				v-if="hasBack"
				class="flex align-center justify-center"
				style="width: 100rpx; height: 100%; flex-shrink: 0;"
				@click="$emit('back')"
			>
				<text class="cuIcon-back text-black"></text>
			</view>
			<view
				v-else
				class="flex align-center justify-center"
				style="width: 100rpx; height: 100%; flex-shrink: 0;"
			></view>

			<!-- 中间标题 -->
			<view
				class="text-black text-xl text-bold text-center text-cut"
				style="position: absolute; left: 120rpx; right: 120rpx;"
			>
				{{ title }}
			</view>

			<!-- 右侧占位，保证标题真正居中 -->
			<view
				class="flex align-center justify-center"
				style="width: 100rpx; height: 100%; flex-shrink: 0;"
			></view>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'TopNav',
		props: {
			title: {
				type: String,
				default: '标题'
			},
			hasBack: {
				type: Boolean,
				default: true
			},
		},
		data() {
			return {
				statusBarHeight: 20,
				navBarHeight: 44
			}
		},
		created() {
			const systemInfo = uni.getSystemInfoSync()
			this.statusBarHeight = systemInfo.statusBarHeight || 20

			// 微信小程序适配胶囊按钮高度
			// #ifdef MP-WEIXIN
			const menuButton = uni.getMenuButtonBoundingClientRect()
			this.navBarHeight =
				(menuButton.top - this.statusBarHeight) * 2 + menuButton.height
			// #endif

			// H5 / App 默认导航高度
			// #ifndef MP-WEIXIN
			this.navBarHeight = 44
			// #endif
		},
		methods: {
			
		}
	}
</script>

