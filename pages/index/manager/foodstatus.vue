<template>
	<view 
	style="padding-bottom: 300rpx;"
	class="container">
		
		<TopNav
		:hasBack="true"
		title="美尔途订餐"
		@back="goBack"
		/>
		
		<view class="bg-white solid margin-sm padding-sm radius">
			<view class="flex justify-between">
				<view
				style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
				class="flex text-black align-center padding-sm justify-center radius">
					菜品管理
				</view>
				<view
				@tap="editfood"
				style=" background-color: #FFD5A7; height: 50rpx; width: 160rpx;"
				class="flex text-black align-center padding-sm justify-center radius">
					{{topright}}
				</view>
			</view>
		</view>
		
		<!-- 编辑板块 -->
		<view 
		v-if="isEditModule"
		class="bg-white solid margin-sm padding-sm radius">
			<view 
			style="height: 100rpx;"
			class="flex align-center justify-between">
				
				<!-- 全选 + 上/下架-->
				<view class="flex justify-between">
					<view
					@tap="toggleSelectAll"
					class="flex align-center">
						<view
						:style="selectAllCheckStyle"
						class="flex align-center justify-center">
							<text
							v-if="isAllFoodSelected"
							style="font-size: 28rpx;"
							class="cuIcon-check text-white"></text>
						</view>
						<text class="margin-left-xs text-black">全选</text>
					</view>
				</view>
				<!-- 上/下架 -->
				<view class="flex">
					<view 
					@tap="handleSetFoodStatus(true)"
					style="
					box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1); 
					background-color: #C9F6C9; height: 70rpx; width: 160rpx;"
					class="radius flex align-center justify-center margin-right text-bold text-black">
						上架
					</view>
					<view 
					@tap="handleSetFoodStatus(false)"
					style="
					box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1);
					background-color: #D9D9D9; height: 70rpx; width: 160rpx;"
					class="radius flex align-center justify-center text-bold text-black">
						下架
					</view>
				</view>
			</view>
		</view>
		
		<!-- 每一样菜品 -->
		<view class="">
			
		</view>
		
		<!-- 底部添加新菜品按钮 -->
		<view 
		v-if="isEditModule"
		style="
		width: 100%;
		position: fixed;
		bottom: 70rpx;"
		class="radius flex justify-center">
			<button
			style="
			width: 80%;
			background-color: #FFC588;
			color: #F98338;
			box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.2);
			"
			class="radius">
				+ 添加新的菜品
			</button>
		</view>
		
		<!-- 菜 -->
		<view
		class="bg-white solid margin-sm padding-sm radius">
			<view 
			v-for="(item, index) in foodList"
			:key="item.id"
			class="">
				<!-- 卡片 -->
				<view 
				style="box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1);"
				class="flex solid padding-sm margin-bottom-sm radius">
					<image 
					style="flex-shrink: 0; width: 150rpx; height: 150rpx;"
					:src="item.image" mode="aspectFill"></image>
					<view 
					style="flex: 1;"
					class="flex flex-direction justify-between padding-sm">
						<view class="text-bold text-black text-lg">
							{{item.name}}
						</view>
						<view v-if="item.status" 
						style="
						width: 120rpx;
						background-color: #C9F6C9;
						"
						class="flex justify-center align-center text-black radius padding-xs">
							已上架
						</view>
						<view v-else 
						style="
						width: 120rpx;
						background-color: #D9D9D9;
						"
						class="flex justify-center align-center text-black radius padding-xs">
							已下架
						</view>
					</view>
					<view 
					style=""
					class="flex align-center">
						<view
						v-if="isEditModule"
						@tap.stop="toggleFoodChecked(item.id)"
						:style="item.checkStyle"
						class="flex align-center justify-center">
							<text
							v-if="isFoodChecked(item.id)"
							style="font-size: 28rpx;"
							class="cuIcon-check text-white"></text>
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<!-- 上/下架确认弹窗 -->
		<view
		v-if="isStatusDialogVisible"
		@tap="cancelFoodStatusDialog"
		style="
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 999;
		background-color: rgba(0, 0, 0, 0.18);"
		class="flex align-center justify-center">
			<view
			@tap.stop
			style="
			width: 660rpx;
			min-height: 500rpx;
			background-color: #FFFFFF;
			position: relative;
			box-sizing: border-box;
			padding: 104rpx 60rpx 60rpx 60rpx;"
			class="radius flex flex-direction align-center">
				<text
				@tap="cancelFoodStatusDialog"
				style="
				position: absolute;
				right: 38rpx;
				top: 34rpx;
				font-size: 44rpx;
				line-height: 44rpx;
				color: #222222;"
				class="cuIcon-close"></text>
				
				<view
				style="
				font-size: 42rpx;
				line-height: 60rpx;"
				class="text-black text-bold">
					{{statusDialogTitle}}
				</view>
				
				<view
				@tap="confirmSetFoodStatus"
				style="
				width: 380rpx;
				height: 78rpx;
				margin-top: 70rpx;
				background-color: #D9D9D9;
				border-radius: 40rpx;
				font-size: 30rpx;"
				class="flex align-center justify-center text-black text-bold">
					确定
				</view>
				
				<view
				@tap="cancelFoodStatusDialog"
				style="
				width: 380rpx;
				height: 78rpx;
				margin-top: 24rpx;
				background-color: #FFFFFF;
				border: 2rpx solid #222222;
				border-radius: 40rpx;
				font-size: 30rpx;"
				class="flex align-center justify-center text-black text-bold">
					再想想
				</view>
			</view>
		</view>
		
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
				topright: '编辑菜品',
				isEditModule: false,
				checkedFoodIds: [],
				isStatusDialogVisible: false,
				pendingFoodStatus: true,
				pendingFoodIds: [],
				activeCheckStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #F55B08; background-color: #F55B08;',
				inactiveCheckStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #999999; background-color: #FFFFFF;',
				foodList: [
					{
						id: 1,
						image: '/static/logo.png',
						name: '水煮肉片',
						status: true, 					// 已上架，已下架
						checked: false,
						checkStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #999999; background-color: #FFFFFF;',
					},
					{
						id: 2,
						image: '/static/logo.png',
						name: '青椒肉丝',
						status: false,
						checked: false,
						checkStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #999999; background-color: #FFFFFF;',
					},
				],
			}
		},
		computed: {
			isAllFoodSelected() {
				if(this.foodList.length === 0) {
					return false;
				}
				if(this.checkedFoodIds.length !== this.foodList.length) {
					return false;
				}
				for(var i = 0; i < this.foodList.length; i++) {
					if(this.checkedFoodIds.indexOf(this.foodList[i].id) === -1) {
						return false;
					}
				}
				return true;
			},
			hasCheckedFood() {
				return this.checkedFoodIds.length > 0;
			},
			statusDialogTitle() {
				if(this.pendingFoodStatus) {
					return '确定上架吗？';
				}
				return '确定下架吗？';
			},
			selectAllCheckStyle() {
				if(this.isAllFoodSelected) {
					return this.activeCheckStyle;
				}
				return this.inactiveCheckStyle;
			}
		},
		methods: {
			goBack() {
				uni.navigateBack({
					delta: 1
				});
			},
			editfood() {
				if(this.topright === '编辑菜品') {
					this.topright = '删除菜品';
					this.isEditModule = true;
					this.initCheckedFoodsByStatus();
				}
				else {
					this.topright = '编辑菜品'
					this.isEditModule = false;
					this.clearCheckedFoods();
				}
			},
			initCheckedFoodsByStatus() {
				var checkedIds = [];
				for(var i = 0; i < this.foodList.length; i++) {
					if(this.foodList[i].status) {
						checkedIds.push(this.foodList[i].id);
					}
				}
				this.checkedFoodIds = checkedIds;
				this.syncFoodCheckedState();
			},
			clearCheckedFoods() {
				this.checkedFoodIds = [];
				this.syncFoodCheckedState();
			},
			isFoodChecked(foodId) {
				return this.checkedFoodIds.indexOf(foodId) !== -1;
			},
			syncFoodCheckedState() {
				for(var i = 0; i < this.foodList.length; i++) {
					var isChecked = this.isFoodChecked(this.foodList[i].id);
					this.foodList[i].checked = isChecked;
					this.foodList[i].checkStyle = isChecked ? this.activeCheckStyle : this.inactiveCheckStyle;
				}
			},
			toggleFoodChecked(foodId) {
				var checkedIndex = this.checkedFoodIds.indexOf(foodId);
				if(checkedIndex === -1) {
					this.checkedFoodIds.push(foodId);
				}
				else {
					this.checkedFoodIds.splice(checkedIndex, 1);
				}
				this.syncFoodCheckedState();
			},
			toggleSelectAll() {
				if(this.isAllFoodSelected) {
					this.checkedFoodIds = [];
					this.syncFoodCheckedState();
					return;
				}
				
				var checkedIds = [];
				for(var i = 0; i < this.foodList.length; i++) {
					checkedIds.push(this.foodList[i].id);
				}
				this.checkedFoodIds = checkedIds;
				this.syncFoodCheckedState();
			},
			handleSetFoodStatus(status) {
				if(!this.hasCheckedFood) {
					uni.showToast({
						title: '请先选择菜品',
						icon: 'none'
					});
					return;
				}
				
				this.pendingFoodStatus = status;
				this.pendingFoodIds = this.checkedFoodIds.slice();
				this.isStatusDialogVisible = true;
			},
			cancelFoodStatusDialog() {
				this.isStatusDialogVisible = false;
				this.pendingFoodIds = [];
			},
			confirmSetFoodStatus() {
				var checkedIds = this.pendingFoodIds.slice();
				var status = this.pendingFoodStatus;
				
				if(checkedIds.length === 0) {
					this.cancelFoodStatusDialog();
					return;
				}
				
				this.requestUpdateFoodStatus(checkedIds, status);
				
				for(var i = 0; i < this.foodList.length; i++) {
					if(checkedIds.indexOf(this.foodList[i].id) !== -1) {
						this.foodList[i].status = status;
					}
				}
				this.initCheckedFoodsByStatus();
				this.cancelFoodStatusDialog();
			},
			requestUpdateFoodStatus(ids, status) {
				// TODO: 后续在这里请求后端，批量更新菜品上下架状态。
			}
		}
	}
</script>

<style>
</style>
