<template>
	<view class="pull-menu" :class="{ 'pull-menu--popup-open': menuAppear }">
		<view class="pull-card-layer">
			<view class="bg-white radius flex align-center pull-card" :style="pullStyle" @touchstart="onStart"
			@touchmove="onMove" @touchend="onEnd">
				<!-- 左侧文字 -->
				<view>
					<view class="text-black text-bold">
						优客来（下拉看菜单图片）
					</view>
					<view
					style="font-size: 22rpx;"
					class="text-grey margin-top-xs">
						提示：请选择菜品加入购物车并下单
					</view>
				</view>
			
				<!-- 右侧图片 -->
				<image class="radius" :src="preImage" mode="widthFix"
					style="width: 100rpx; height: 100rpx;">
				</image>
			</view>
		</view>

		<view
			v-if="menuAppear"
			@tap="closeMenu"
			style="position: fixed; left: 0; right: 0; top: 0; bottom: 0; z-index: 100200; background-color: rgba(0, 0, 0, 0.45); display: flex; align-items: center; justify-content: center;"
		>
			<view @tap.stop class="menu-image" style="width: 100%;">
				<image :src="bigImage" mode="widthFix" style="width: 100%;"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'PullToLookMenu',
		props: {
			preImage: {
				type: String,
				default: '#'
			},
			bigImage: {
				type: String,
				default: '#'
			}
		},
		data() {
			return {
				isPull: false,
				startY: 0, // 起始Y轴
				changeY: 0, // 移动了多少
				maxPullDistance: 70, // 最大下拉距离
				triggerDistance: 60, // 触发显示菜单图片的距离
				menuAppear: false, // 菜单图片是否处于展示状态
			};
		},
		computed: {
			pullStyle() {
				// 不能上滑动，只能下拉，且不能超过最大下拉距离
				const y = Math.max(0, Math.min(this.changeY, this.maxPullDistance));
				return `transform: translate3d(0, ${y}px, 0); transition: ${this.isPull ? 'none' : 'transform 0.3s ease'};`;
			},
		},
		methods: {
			onStart(e) {
				this.isPull = true;
				this.startY = e.touches[0].clientY;
			},
			
			onMove(e) {
				this.changeY_ = e.touches[0].clientY - this.startY;
				if (this.changeY_ > this.maxPullDistance || this.changeY_ < 0) {
					return;
				}
				this.changeY = e.touches[0].clientY - this.startY;
				if (this.changeY > 0 && this.changeY <= this.maxPullDistance) {
					this.isPull = true;
				}
			
			},
			
			onEnd() {
				if (this.changeY > this.triggerDistance) {
					this.menuAppear = true;
					this.isPull = false;
					this.changeY = 0;
				} else {
					this.isPull = false;
					this.changeY = 0;
				}
				// console.log('是否显示菜单', this.menuAppear);
			},
			
			closeMenu() {
				this.menuAppear = false;
				// console.log('menuAppear', this.menuAppear);
			},
		},
	};
</script>

<style>
	.pull-menu {
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		height: 430rpx;
		z-index: 40;
		pointer-events: none;
	}

	.pull-menu--popup-open {
		pointer-events: auto;
	}

	.pull-card-layer {
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		height: 430rpx;
		pointer-events: none;
	}

	.pull-card {
		position: absolute;
		left: 20rpx;
		right: 20rpx;
		top: 150rpx;
		z-index: 40;
		height: 150rpx;
		box-sizing: border-box;
		justify-content: space-between;
		padding: 0 30rpx;
		pointer-events: auto;
	}
</style>
