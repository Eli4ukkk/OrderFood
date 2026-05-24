<template>
	<view class="bg-white food-panel">
		<view class="food-panel__inner bg-white">
			<scroll-view scroll-y show-scrollbar class="food-scroll">
				<view class="food-list">
					<view v-for="(item, index) in foodList" :key="index"
						class="food-item solid-bottom">
						<image :src="item.image" mode="aspectFill" class="food-image radius"></image>
		
						<view class="food-info">
							<text class="text-black text-cut">{{item.title}}</text>
							<text class="text-price text-black">{{item.price}}</text>
						</view>
		
						<view class="quantity-actions">
							<!-- 减号 -->
							<view @click="subItem(item, index)" v-if="item.number" class="quantity-button quantity-button--outline text-xl">-</view>
		
							<!-- 数量 -->
							<view v-if="item.number" class="quantity-count text-xl text-black">{{item.number}}</view>
		
							<!-- 加号 -->
							<view @click="addItem(item, index)" class="quantity-button quantity-button--solid text-xl text-white">+</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'CenterFoodDisplay',
		props: {
			foodList: {
				type: Array,
				default: () => [],
			},
			backendType: {
				type: String, // 根据字符串来进行后端查询
				default: '全部'
			}
		},
		data() {
			return {
				
			};
		},
		methods: {
			subItem(item, index) {
				this.$emit('subItem', {
					item,
					index,
				});
			},
			
			addItem(item, index) {
				this.$emit('addItem', {
					item,
					index,
				});
				this.showToastAdd('添加成功');
			},
			
			showToastAdd(message) {
				uni.showToast({
					title: message,
					icon: 'none',
					duration: 1000
				})
			}
		}
	}
</script>

<style>
	.food-panel {
		flex: 1;
		width: 100%;
		min-width: 0;
		height: 100%;
		overflow: hidden;
		box-sizing: border-box;
		display: flex;
	}

	.food-panel__inner {
		width: 100%;
		height: 100%;
		min-width: 0;
		box-sizing: border-box;
		padding-left: 20rpx;
		padding-right: 20rpx;
	}

	.food-scroll {
		width: 100%;
		height: 100%;
		box-sizing: border-box;
	}

	.food-list {
		width: 100%;
		box-sizing: border-box;
	}

	.food-item {
		display: flex;
		align-items: center;
		width: 100%;
		min-width: 0;
		min-height: 170rpx;
		box-sizing: border-box;
		padding-top: 20rpx;
		padding-bottom: 20rpx;
	}

	.food-image {
		width: 130rpx;
		height: 130rpx;
		flex-shrink: 0;
	}

	.food-info {
		flex: 1;
		width: 0;
		height: 130rpx;
		min-width: 0;
		overflow: hidden;
		box-sizing: border-box;
		padding-left: 20rpx;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.quantity-actions {
		width: 150rpx;
		flex-shrink: 0;
		display: flex;
		align-items: center;
		justify-content: flex-end;
		align-self: flex-end;
	}

	.quantity-button,
	.quantity-count {
		width: 50rpx;
		height: 50rpx;
		line-height: 50rpx;
		text-align: center;
		border-radius: 50%;
		box-sizing: border-box;
		flex-shrink: 0;
	}

	.quantity-button--outline {
		color: #5D9A7D;
		border: 1px solid #5D9A7D;
	}

	.quantity-button--solid {
		background-color: #5D9A7D;
		border: 1px solid #5D9A7D;
	}
</style>
