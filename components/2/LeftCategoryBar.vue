<template>

	<scroll-view scroll-y class="category-bar bg-white">
		<block
		v-for="(item, index) in categoryList"
		:key="index">
			<view
			v-if="categoryIndex === index"
			@tap="isActiveChange(index, item)"
			class="category-item isActive solid-bottom solid-top text-center text-bold text-black"
			style="border-left-color: #5D9A7D;">
				{{item.name || item}}
			</view>
			<view
			v-else
			@tap="isActiveChange(index, item)"
			class="category-item solid-bottom solid-top text-center text-bold text-black"
			style="border-left-color: transparent;">
				{{item.name || item}}
			</view>
		</block>
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
		isActiveChange(index, item) {
			var categoryId = '';
			var categoryType = item;
			if(item && item.name) {
				categoryId = item.id;
				categoryType = item.name;
			}
			this.$emit('categoryChange', {
				index: index,
				id: categoryId,
				type: categoryType
			});
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
