<template>
	<view class="order-search flex bg-white">
		<view class="order-search__inner flex align-center">
			<!-- 点餐 -->
			<view class="order-search__tab order-food text-bold text-center">
				点餐
				<view class="order-search__underline">
				</view>
			</view>
		
			<!-- 全部 -->
			<view class="order-search__tab order-food text-bold text-center">
				{{categoryType}}
			</view>
		
			<!-- 搜索 -->
			<view class="search-bar flex align-center">
				<!-- 搜索框 -->
				<view class="search-input flex align-center">
					<text class="cuIcon-search search-icon"></text>
					<input
						:value="keyword"
						@input="handleSearchInput"
						confirm-type="search"
						placeholder="搜索"
						placeholder-class="search-placeholder"
						class="search-native-input" />
				</view>
		
				<!-- 按钮 -->
				<view
				@tap="searchChange"
				class="search-button flex align-center justify-center text-white">
					搜索
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	name: 'OrderAllSearch',
	props: {
		categoryType: {
			type: String,
			default: '全部'
		}
	},
	
	data() {
		return {
			keyword: '',
			searchTimeId: null, // 搜索防抖的定时器ID
		};
	},
	methods: {
		handleSearchInput(e) {
			this.keyword = e.detail.value;
			this.searchChange();
		},
		searchChange() {
			// 防抖
			// console.log('searchChange 被触发了');
			clearTimeout(this.searchTimeId);
			this.searchTimeId = setTimeout(() => {
				console.log('当前搜索栏', this.keyword);
				//搜索逻辑
		
		
		
		
			}, 500);
		},
	},
};
</script>

<style>
	.order-search {
		position: relative;
		height: 90rpx;
		padding: 10rpx 20rpx;
		box-sizing: border-box;
		flex-shrink: 0;
		z-index: 10;
	}

	.order-search__inner {
		width: 100%;
		min-width: 0;
	}

	.order-search__tab {
		position: relative;
		width: 110rpx;
		height: 70rpx;
		line-height: 70rpx;
		color: #000;
		flex-shrink: 0;
	}

	.order-search__underline {
		position: absolute;
		left: 36rpx;
		right: 36rpx;
		bottom: 10rpx;
		height: 4rpx;
		background-color: #000;
	}

	.search-bar {
		flex: 1;
		min-width: 0;
	}

	.search-input {
		flex: 1;
		min-width: 0;
		height: 70rpx;
		border-radius: 35rpx;
		background-color: #F5F5F5;
		padding: 0 20rpx;
		box-sizing: border-box;
	}

	.search-button {
		width: 120rpx;
		height: 70rpx;
		margin-left: 12rpx;
		flex-shrink: 0;
		border-radius: 35rpx;
		background-color: #5D9A7D;
	}

	.search-icon {
		color: #909399;
		font-size: 30rpx;
		margin-right: 10rpx;
		flex-shrink: 0;
	}

	.search-native-input {
		flex: 1;
		min-width: 0;
		height: 70rpx;
		font-size: 28rpx;
		color: #303133;
	}

	.search-placeholder {
		color: #909399;
	}
</style>
