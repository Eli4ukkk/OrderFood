<template>
	<view style="position: relative; overflow: hidden;" class="margin-bottom-sm radius">
		<view v-if="isEditModule" @tap.stop="$emit('delete', item)" style="
		position: absolute;
		left: 0;
		top: 0;
		bottom: 0;
		width: 150rpx;
		z-index: 1;
		background-color: #F55B08;" class="flex flex-direction align-center justify-center text-white radius">
			<text style="font-size: 38rpx; line-height: 42rpx;" class="cuIcon-delete"></text>
			<text style="font-size: 24rpx; line-height: 34rpx; margin-top: 6rpx;">
				删除
			</text>
		</view>
		<!-- 卡片 -->
		<view @touchstart="$emit('touch-start', $event, item)" @touchmove="$emit('touch-move', $event, item)"
			@touchend="$emit('touch-end', $event, item)" @touchcancel="$emit('touch-cancel', item)"
			:style="cardTransformStyle"
			class="flex solid padding-sm radius">
			<image style="flex-shrink: 0; width: 150rpx; height: 150rpx;" :src="item.image" mode="aspectFill">
			</image>
			<view style="flex: 1;" class="flex flex-direction justify-between padding-sm">
				<view class="text-bold text-black text-lg">
					{{item.name}}
				</view>
				<view v-if="item.status" style="
				width: 120rpx;
				background-color: #C9F6C9;
				" class="flex justify-center align-center text-black radius padding-xs">
					已上架
				</view>
				<view v-else style="
				width: 120rpx;
				background-color: #D9D9D9;
				" class="flex justify-center align-center text-black radius padding-xs">
					已下架
				</view>
			</view>
			<view style="" class="flex align-center">
				<view v-if="isEditModule" @tap.stop="$emit('toggle-check', item.id)" :style="item.checkStyle"
					class="flex align-center justify-center">
					<text v-if="item.checked" style="font-size: 28rpx;"
						class="cuIcon-check text-white"></text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'FoodCard',
		props: {
			item: Object,
			isEditModule: Boolean
		},
		computed: {
			cardTransformStyle() {
				var swipeOffset = this.isEditModule && this.item ? this.item.swipeOffset : 0;
				var transition = this.item && this.item.isTouching ? 'none' : 'transform 220ms ease';

				return 'position: relative; z-index: 2; background-color: #FFFFFF; box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1); transform: translateX(' +
					swipeOffset + 'rpx); transition: ' + transition + ';';
			}
		}
	}
</script>
