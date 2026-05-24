<template>

	<scroll-view scroll-y class="category-bar bg-white">
		<view 
			v-for="(item, index) in categoryList" :key="index"
			@click="isActiveChange(index, item)" :class="{ 'isActive': categoryIndex === index }"
			class="category-item solid-bottom solid-top text-center text-bold text-black"
			:style="{borderLeftColor: categoryIndex == index ? '#5D9A7D' : 'transparent'}">
			{{item}}
		</view>
	</scroll-view>

</template>

<script>
export default {
	name: 'LeftCategoryBar',
	props: {
		categoryIndex: {
			type: Number,
			default: 0
		},
		categoryList: {
			type: Array,
			default: () => []
		}
	},
	
	data() {
		return {
		}
	},
	methods: {
		isActiveChange(index, type) {
			this.$emit('categoryChange', { index, type });
		},
	},
}
</script>

<style>
	.category-bar {
		width: 150rpx;
		height: 100%;
		flex-shrink: 0;
		box-sizing: border-box;
	}

	.category-item {
		width: 100%;
		height: 90rpx;
		line-height: 90rpx;
		box-sizing: border-box;
		border-left: 10rpx solid transparent;
	}

	.isActive {
		box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.15);
	}
</style>
