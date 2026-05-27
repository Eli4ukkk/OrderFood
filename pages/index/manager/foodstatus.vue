<template>
	<view style="padding-bottom: 300rpx;" class="container">

		<TopNav :hasBack="true" title="美尔途订餐" @back="goBack" />

		<view class="bg-white solid margin-sm padding-sm radius">
			<view class="flex justify-between">
				<view style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
					class="flex text-black align-center padding-sm justify-center radius">
					菜品管理
				</view>
				<view @tap="editfood" style=" background-color: #FFD5A7; height: 50rpx; width: 160rpx;"
					class="flex text-black align-center padding-sm justify-center radius">
					{{topright}}
				</view>
			</view>
		</view>

		<!-- 编辑板块 -->
		<view v-if="isEditModule" class="bg-white solid margin-sm padding-sm radius">
			<view style="height: 100rpx;" class="flex align-center justify-between">

				<!-- 全选 + 上/下架-->
				<view class="flex justify-between">
					<view @tap="toggleSelectAll" class="flex align-center">
						<view :style="selectAllCheckStyle" class="flex align-center justify-center">
							<text v-if="isAllFoodSelected" style="font-size: 28rpx;"
								class="cuIcon-check text-white"></text>
						</view>
						<text class="margin-left-xs text-black">全选</text>
					</view>
				</view>
				<!-- 上/下架 -->
				<view class="flex">
					<view @tap="handleSetFoodStatus(true)" style="
					box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1); 
					background-color: #C9F6C9; height: 70rpx; width: 160rpx;"
						class="radius flex align-center justify-center margin-right text-bold text-black">
						上架
					</view>
					<view @tap="handleSetFoodStatus(false)" style="
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
		<view v-if="isEditModule" style="
		width: 100%;
		position: fixed;
		bottom: 70rpx;" class="radius flex justify-center">
			<button @tap="openFoodDialog" style="
			width: 80%;
			background-color: #FFC588;
			color: #F98338;
			box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.2);
			" class="radius">
				+ 添加新的菜品
			</button>
		</view>

		<!-- 菜 -->
		<view class="bg-white solid margin-sm padding-sm radius">
			<view v-for="(item, index) in foodList" :key="item.id" style="position: relative; overflow: hidden;"
				class="margin-bottom-sm radius">
				<view v-if="isEditModule" @tap.stop="handleFoodDeleteTap(item)" style="
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
				<view @touchstart="handleFoodTouchStart($event, item)" @touchmove="handleFoodTouchMove($event, item)"
					@touchend="handleFoodTouchEnd($event, item)" @touchcancel="handleFoodTouchCancel(item)"
					:style="'position: relative; z-index: 2; background-color: #FFFFFF; box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1); transform: translateX(' + (isEditModule ? item.swipeOffset : 0) + 'rpx); transition: ' + (item.isTouching ? 'none' : 'transform 220ms ease') + ';'"
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
						<view v-if="isEditModule" @tap.stop="toggleFoodChecked(item.id)" :style="item.checkStyle"
							class="flex align-center justify-center">
							<text v-if="isFoodChecked(item.id)" style="font-size: 28rpx;"
								class="cuIcon-check text-white"></text>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- 新增菜品弹窗 -->
		<view v-if="showFoodDialog" @tap="closeFoodDialog" style="
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 998;
		background-color: rgba(0, 0, 0, 0.08);" class="margin-top flex align-center justify-center">
			<view @tap.stop style="
			width: 700rpx;
			min-height: 880rpx;
			background-color: #FFFFFF;
			position: relative;
			box-sizing: border-box;
			padding: 120rpx 36rpx 60rpx 36rpx;" class="radius flex flex-direction align-center">
				<text @tap="closeFoodDialog" style="
				position: absolute;
				right: 36rpx;
				top: 34rpx;
				font-size: 48rpx;
				line-height: 48rpx;
				color: #111111;" class="cuIcon-close"></text>

				<view @tap="chooseFoodImage" style="
				width: 280rpx;
				height: 280rpx;
				background-color: #D9D9D9;
				border-radius: 18rpx;
				overflow: hidden;" class="flex flex-direction align-center justify-center">
					<image v-if="foodForm.image" :src="foodForm.image" style="width: 280rpx; height: 280rpx;"
						mode="aspectFill"></image>
					<view v-else class="flex flex-direction align-center justify-center">
						<text style="font-size: 58rpx; line-height: 64rpx; color: #111111;" class="cuIcon-add"></text>
						<text style="font-size: 28rpx; line-height: 40rpx; color: #555555; margin-top: 24rpx;">
							点击上传菜品主图
						</text>
					</view>
				</view>

				<view style="
				width: 100%;
				margin-top: 34rpx;
				background-color: #D9D9D9;
				border-radius: 12rpx;
				overflow: hidden;">
					<view
						style="height: 82rpx; padding: 0 20rpx; border-bottom: 2rpx dashed #AAAAAA; box-sizing: border-box;"
						class="flex align-center justify-between">
						<text style="font-size: 30rpx; color: #111111;">菜品名称</text>
						<input v-model="foodForm.name" placeholder="请输入" placeholder-style="color: #666666;"
							style="width: 360rpx; height: 82rpx; line-height: 82rpx; text-align: right; font-size: 28rpx; color: #333333;" />
					</view>
					<view @tap="openCategoryDialog"
						style="height: 82rpx; padding: 0 20rpx; border-bottom: 2rpx dashed #AAAAAA; box-sizing: border-box;"
						class="flex align-center justify-between">
						<text style="font-size: 30rpx; color: #111111;">菜品分类</text>
						<view class="flex align-center">
							<text style="font-size: 28rpx; color: #555555;">
								{{foodForm.categoryName}}
							</text>
							<text style="font-size: 42rpx; color: #333333; margin-left: 14rpx;"
								class="cuIcon-right"></text>
						</view>
					</view>
					<view style="height: 82rpx; padding: 0 20rpx; box-sizing: border-box;"
						class="flex align-center justify-between">
						<text style="font-size: 30rpx; color: #111111;">菜品价格</text>
						<input v-model="foodForm.price" type="digit" placeholder="0" placeholder-style="color: #555555;"
							style="width: 300rpx; height: 82rpx; line-height: 82rpx; text-align: right; font-size: 28rpx; color: #333333;" />
					</view>
				</view>

				<view @tap="confirmFoodForm" style="
				width: 390rpx;
				height: 86rpx;
				margin-top: 40rpx;
				background-color: #FFC588;
				border-radius: 48rpx;
				font-size: 34rpx;
				color: #F55B08;" class="flex align-center justify-center text-bold">
					确定
				</view>
			</view>
		</view>

		<!-- 分类选择 / 管理弹窗 -->
		<view v-if="showCategoryDialog" style="
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 1000;
		background-color: #FFFFFF;
		box-sizing: border-box;
		padding: 0rpx 38rpx 36rpx 38rpx;" class="flex flex-direction">
			<TopNav :hasBack="false" title="分类管理"></TopNav>

			<view style="height: 76rpx; flex-shrink: 0;" class="flex align-center justify-between">
				<view @tap="closeCategoryDialog" class="flex align-center">
					<text style="font-size: 42rpx; line-height: 42rpx; color: #111111;" class="cuIcon-back"></text>
					<text style="font-size: 30rpx; line-height: 42rpx; color: #111111; margin-left: 24rpx;">
						菜品分类
					</text>
				</view>
				<text @tap="handleDeleteCategoryTap" style="font-size: 30rpx; line-height: 42rpx; color: #F55B08;">
					删除分类
				</text>
			</view>

			<view style="flex: 1; min-height: 0;" class="flex flex-direction">
				<view @tap="toggleCategorySelectAll" style="height: 80rpx; flex-shrink: 0;" class="flex align-center">
					<view v-if="isAllCategorySelected"
						style="width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #F55B08; background-color: #F55B08;"
						class="flex align-center justify-center">
						<text style="font-size: 28rpx;" class="cuIcon-check text-white"></text>
					</view>
					<view v-else
						style="width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #D9D9D9; background-color: #D9D9D9;"
						class="flex align-center justify-center">
					</view>
					<text style="font-size: 30rpx; line-height: 42rpx; color: #666666; margin-left: 12rpx;">
						全选
					</text>
				</view>

				<scroll-view scroll-y style="height: 560rpx; flex-shrink: 0;">
					<view v-for="item in categoryList" :key="item.id" style="height: 86rpx;" class="flex align-center">
						<view v-if="item.checked" @tap.stop="toggleCategoryChecked(item)"
							style="width: 44rpx; height: 44rpx; border-radius: 10rpx; border: 2rpx solid #F55B08; background-color: #F55B08; flex-shrink: 0;"
							class="flex align-center justify-center">
							<text style="font-size: 28rpx;" class="cuIcon-check text-white"></text>
						</view>
						<view v-else @tap.stop="toggleCategoryChecked(item)"
							style="width: 44rpx; height: 44rpx; border-radius: 10rpx; border: 2rpx solid #D9D9D9; background-color: #D9D9D9; flex-shrink: 0;"
							class="flex align-center justify-center">
						</view>
						<text @tap="selectCategory(item)"
							style="font-size: 34rpx; line-height: 48rpx; color: #111111; margin-left: 12rpx; font-weight: bold;">
							{{item.name}}
						</text>
					</view>
				</scroll-view>
			</view>

			<view style="flex-shrink: 0;" class="flex flex-direction align-center">
				<view @tap="openAddCategoryDialog" style="
				width: 420rpx;
				height: 88rpx;
				border: 2rpx solid #F55B08;
				border-radius: 48rpx;
				font-size: 32rpx;
				line-height: 88rpx;
				color: #F55B08;" class="text-center text-bold">
					新增分类
				</view>
				<view @tap="saveCategorySelect" style="
				width: 420rpx;
				height: 88rpx;
				margin-top: 20rpx;
				background-color: #FFC588;
				border-radius: 48rpx;
				font-size: 32rpx;
				line-height: 88rpx;
				color: #F55B08;" class="text-center text-bold">
					保存修改
				</view>
			</view>
		</view>

		<!-- 新增分类小弹窗 -->
		<view v-if="showAddCategoryDialog" @tap="closeAddCategoryDialog" style="
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 1001;
		background-color: rgba(0, 0, 0, 0.18);" class="flex align-center justify-center">
			<view @tap.stop style="
			width: 660rpx;
			min-height: 320rpx;
			background-color: #FFFFFF;
			border-radius: 18rpx;
			box-sizing: border-box;
			padding: 28rpx 30rpx 24rpx 30rpx;" class="flex flex-direction">
				<input v-model="newCategoryName" placeholder="请输入新菜品分类..."
					placeholder-style="color: #111111; font-weight: bold;" style="
				width: 100%;
				height: 72rpx;
				line-height: 72rpx;
				font-size: 30rpx;
				color: #111111;
				font-weight: bold;" />

				<view style="flex: 1;">
				</view>

				<view @tap="confirmAddCategory" style="
				width: 230rpx;
				height: 66rpx;
				background-color: #D9D9D9;
				border-radius: 12rpx;
				font-size: 30rpx;
				line-height: 66rpx;
				color: #111111;
				align-self: center;" class="text-center text-bold">
					完成
				</view>
			</view>
		</view>

		<!-- 删除确认弹窗 -->
		<view v-if="isDeleteDialogVisible" @tap="cancelFoodDeleteDialog" style="
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 999;
		background-color: rgba(0, 0, 0, 0.18);" class="flex align-center justify-center">
			<view @tap.stop style="
				width: 660rpx;
				min-height: 500rpx;
				background-color: #FFFFFF;
				position: relative;
				box-sizing: border-box;
				padding: 104rpx 60rpx 60rpx 60rpx;" class="radius flex flex-direction align-center">
				<text @tap="cancelFoodDeleteDialog" style="
				position: absolute;
				right: 38rpx;
				top: 34rpx;
				font-size: 44rpx;
				line-height: 44rpx;
				color: #222222;" class="cuIcon-close"></text>

				<view style="
				font-size: 42rpx;
				line-height: 60rpx;" class="text-black text-bold">
					确定删除吗？
				</view>

				<view v-if="deleteTargetFood" style="margin-top: 26rpx; font-size: 28rpx; line-height: 40rpx;"
					class="text-gray text-center">
					{{deleteTargetFood.name}}
				</view>

				<view @tap="confirmFoodDelete" style="
				width: 380rpx;
				height: 78rpx;
				margin-top: 70rpx;
				background-color: #D9D9D9;
				border-radius: 40rpx;
				font-size: 30rpx;" class="flex align-center justify-center text-black text-bold">
					确定
				</view>

				<view @tap="cancelFoodDeleteDialog" style="
				width: 380rpx;
				height: 78rpx;
				margin-top: 24rpx;
				background-color: #FFFFFF;
				border: 2rpx solid #222222;
				border-radius: 40rpx;
				font-size: 30rpx;" class="flex align-center justify-center text-black text-bold">
					再想想
				</view>
			</view>
		</view>

		<!-- 上/下架确认弹窗 -->
		<view v-if="isStatusDialogVisible" @tap="cancelFoodStatusDialog" style="
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 999;
		background-color: rgba(0, 0, 0, 0.18);" class="flex align-center justify-center">
			<view @tap.stop style="
			width: 660rpx;
			min-height: 500rpx;
			background-color: #FFFFFF;
			position: relative;
			box-sizing: border-box;
			padding: 104rpx 60rpx 60rpx 60rpx;" class="radius flex flex-direction align-center">
				<text @tap="cancelFoodStatusDialog" style="
				position: absolute;
				right: 38rpx;
				top: 34rpx;
				font-size: 44rpx;
				line-height: 44rpx;
				color: #222222;" class="cuIcon-close"></text>

				<view style="
				font-size: 42rpx;
				line-height: 60rpx;" class="text-black text-bold">
					{{statusDialogTitle}}
				</view>

				<view @tap="confirmSetFoodStatus" style="
				width: 380rpx;
				height: 78rpx;
				margin-top: 70rpx;
				background-color: #D9D9D9;
				border-radius: 40rpx;
				font-size: 30rpx;" class="flex align-center justify-center text-black text-bold">
					确定
				</view>

				<view @tap="cancelFoodStatusDialog" style="
				width: 380rpx;
				height: 78rpx;
				margin-top: 24rpx;
				background-color: #FFFFFF;
				border: 2rpx solid #222222;
				border-radius: 40rpx;
				font-size: 30rpx;" class="flex align-center justify-center text-black text-bold">
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
		onLoad() {
			var systemInfo = uni.getSystemInfoSync();
			this.windowWidth = systemInfo.windowWidth || 375;
		},
		data() {
			return {
				topright: '编辑菜品',
				isEditModule: false,
				checkedFoodIds: [],
				isStatusDialogVisible: false,
				isDeleteDialogVisible: false,
				showFoodDialog: false,
				showCategoryDialog: false,
				showAddCategoryDialog: false,
				newCategoryName: '',
				pendingFoodStatus: true,
				pendingFoodIds: [],
				deleteTargetFood: null,
				touchStartX: 0,
				touchStartY: 0,
				touchStartOffset: 0,
				windowWidth: 375,
				foodDeleteOffset: 150,
				activeCheckStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #F55B08; background-color: #F55B08;',
				inactiveCheckStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #999999; background-color: #FFFFFF;',
				// TODO: 后续替换为 GET /categories，分类 id 使用后端返回值
				categoryList: [{
						id: 1,
						name: '荤菜',
						checked: false,
					},
					{
						id: 2,
						name: '素菜',
						checked: false,
					},
				],
				selectedCategoryId: '',
				selectedCategoryName: '',
				deleteCategoryIds: [],
				foodForm: {
					image: '',
					name: '',
					categoryId: '',
					categoryName: '未分类',
					price: '',
				},
				foodList: [{
						id: 1,
						image: '/static/logo.png',
						name: '水煮肉片',
						status: true, // 已上架，已下架
						checked: false,
						checkStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #999999; background-color: #FFFFFF;',
						swipeOffset: 0,
						isTouching: false,
					},
					{
						id: 2,
						image: '/static/logo.png',
						name: '青椒肉丝',
						status: false,
						checked: false,
						checkStyle: 'width: 44rpx; height: 44rpx; border-radius: 50%; border: 2rpx solid #999999; background-color: #FFFFFF;',
						swipeOffset: 0,
						isTouching: false,
					},
				],
			}
		},
		computed: {
			isAllFoodSelected() {
				if (this.foodList.length === 0) {
					return false;
				}
				if (this.checkedFoodIds.length !== this.foodList.length) {
					return false;
				}
				for (var i = 0; i < this.foodList.length; i++) {
					if (this.checkedFoodIds.indexOf(this.foodList[i].id) === -1) {
						return false;
					}
				}
				return true;
			},
			hasCheckedFood() {
				return this.checkedFoodIds.length > 0;
			},
			statusDialogTitle() {
				if (this.pendingFoodStatus) {
					return '确定上架吗？';
				}
				return '确定下架吗？';
			},
			selectAllCheckStyle() {
				if (this.isAllFoodSelected) {
					return this.activeCheckStyle;
				}
				return this.inactiveCheckStyle;
			},
			isAllCategorySelected() {
				if (this.categoryList.length === 0) {
					return false;
				}
				if (this.deleteCategoryIds.length !== this.categoryList.length) {
					return false;
				}
				for (var i = 0; i < this.categoryList.length; i++) {
					if (this.deleteCategoryIds.indexOf(this.categoryList[i].id) === -1) {
						return false;
					}
				}
				return true;
			}
		},
		methods: {
			goBack() {
				const pages = getCurrentPages();
				if (pages.length > 1) {
					uni.navigateBack({
						delta: 1
					});
					return;
				}
				uni.navigateTo({
					url: '/pages/index/manager/3'
				});
			},
			editfood() {
				if (this.topright === '编辑菜品') {
					this.topright = '编辑中';
					this.isEditModule = true;
					this.initCheckedFoodsByStatus();
					this.resetFoodSwipeState();
				} else {
					this.topright = '编辑菜品'
					this.isEditModule = false;
					this.clearCheckedFoods();
					this.resetFoodSwipeState();
				}
			},
			openFoodDialog() {
				this.resetFoodForm();
				this.showFoodDialog = true;
			},
			closeFoodDialog() {
				this.showFoodDialog = false;
				this.resetFoodForm();
			},
			resetFoodForm() {
				this.foodForm = {
					image: '',
					name: '',
					categoryId: '',
					categoryName: '未分类',
					price: '',
				};
				this.selectedCategoryId = '';
				this.selectedCategoryName = '';
				this.deleteCategoryIds = [];
				this.syncCategoryCheckedState();
			},
			chooseFoodImage() {
				var self = this;
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['album', 'camera'],
					success: function(res) {
						if (res.tempFilePaths && res.tempFilePaths.length > 0) {
							self.foodForm.image = res.tempFilePaths[0];
						}
					}
				});
			},
			openCategoryDialog() {
				this.selectedCategoryId = this.foodForm.categoryId;
				if (this.foodForm.categoryName === '未分类') {
					this.selectedCategoryName = '';
				} else {
					this.selectedCategoryName = this.foodForm.categoryName;
				}
				this.showCategoryDialog = true;
			},
			closeCategoryDialog() {
				this.showCategoryDialog = false;
			},
			selectCategory(item) {
				console.log('选择分类', item);
				if (item.checked === false) {
					item.checked = true;
				}
				this.selectedCategoryId = item.id;
				this.selectedCategoryName = item.name;
			},
			isCategoryChecked(categoryId) {
				return this.deleteCategoryIds.indexOf(categoryId) !== -1;
			},
			syncCategoryCheckedState() {
				for (var i = 0; i < this.categoryList.length; i++) {
					this.categoryList[i].checked = this.isCategoryChecked(this.categoryList[i].id);
				}
			},
			toggleCategoryChecked(item) {
				var checkedIndex = this.deleteCategoryIds.indexOf(item.id);
				if (checkedIndex === -1) {
					this.deleteCategoryIds.push(item.id);
				} else {
					this.deleteCategoryIds.splice(checkedIndex, 1);
				}
				this.selectCategory(item);
				this.syncCategoryCheckedState();
			},
			toggleCategorySelectAll() {
				if (this.isAllCategorySelected) {
					this.deleteCategoryIds = [];
					this.syncCategoryCheckedState();
					return;
				}

				var categoryIds = [];
				for (var i = 0; i < this.categoryList.length; i++) {
					categoryIds.push(this.categoryList[i].id);
				}
				this.deleteCategoryIds = categoryIds;
				this.syncCategoryCheckedState();

				if (this.deleteCategoryIds.length === 1) {
					this.selectCategory(this.categoryList[0]);
				}
			},
			saveCategorySelect() {
				if (this.deleteCategoryIds.length > 1) {
					uni.showToast({
						title: '菜品只能选择一个分类',
						icon: 'none'
					});
					return;
				}

				this.foodForm.categoryId = this.selectedCategoryId;
				this.foodForm.categoryName = this.selectedCategoryName || '未分类';
				this.showCategoryDialog = false;
			},
			handleDeleteCategoryTap() {
				if (this.deleteCategoryIds.length === 0) {
					uni.showToast({
						title: '请先选择分类',
						icon: 'none'
					});
					return;
				}

				var deleteIds = this.deleteCategoryIds.slice();
				var nextCategoryList = [];
				for (var i = 0; i < this.categoryList.length; i++) {
					if (deleteIds.indexOf(this.categoryList[i].id) === -1) {
						nextCategoryList.push(this.categoryList[i]);
					}
				}

				// TODO: 后续替换为 DELETE /categories 或批量删除接口
				this.categoryList = nextCategoryList;

				if (deleteIds.indexOf(this.foodForm.categoryId) !== -1) {
					this.foodForm.categoryId = '';
					this.foodForm.categoryName = '未分类';
				}
				if (deleteIds.indexOf(this.selectedCategoryId) !== -1) {
					this.selectedCategoryId = '';
					this.selectedCategoryName = '';
				}

				this.deleteCategoryIds = [];
				this.syncCategoryCheckedState();
				this.syncCategoryStorage();
			},
			openAddCategoryDialog() {
				this.newCategoryName = '';
				this.showAddCategoryDialog = true;
			},
			closeAddCategoryDialog() {
				this.showAddCategoryDialog = false;
				this.newCategoryName = '';
			},
			confirmAddCategory() {
				var categoryName = this.newCategoryName ? this.newCategoryName.trim() : '';
				if (!categoryName) {
					// 不能为空
					return;
				}

				for (var i = 0; i < this.categoryList.length; i++) {
					// 类型重名
					if (this.categoryList[i].name === categoryName) {
						return;
					}
				}

				// TODO: 后续替换为 POST /categories，id 使用后端返回值
				var newCategory = {
					id: Date.now(),
					name: categoryName,
					checked: false,
				};

				this.categoryList.push(newCategory);
				this.deleteCategoryIds = [];
				this.syncCategoryCheckedState();
				this.selectCategory(newCategory);
				this.syncCategoryStorage();
				this.closeAddCategoryDialog();
			},
			syncCategoryStorage() {
				var categoryListWithoutChecked = [];
				for (var i = 0; i < this.categoryList.length; i++) {
					categoryListWithoutChecked.push({
						id: this.categoryList[i].id,
						name: this.categoryList[i].name,
					});
				}
				// TODO: 后续分类数据改为后端维护，这里替换为接口刷新或删除本地缓存逻辑
				uni.setStorageSync('categoryList', categoryListWithoutChecked);
			},
			confirmFoodForm() {
				var foodName = this.foodForm.name ? this.foodForm.name.trim() : '';
				var foodPrice = this.foodForm.price ? String(this.foodForm.price).trim() : '';

				if (!foodName) {
					uni.showToast({
						title: '请输入菜品名称',
						icon: 'none'
					});
					return;
				}
				if (!foodPrice) {
					uni.showToast({
						title: '请输入菜品价格',
						icon: 'none'
					});
					return;
				}
				if (isNaN(Number(foodPrice)) || Number(foodPrice) < 0) {
					uni.showToast({
						title: '请输入正确价格',
						icon: 'none'
					});
					return;
				}

				var newFood = {
					id: this.createLocalFoodId(),
					image: this.foodForm.image || '/static/logo.png',
					name: foodName,
					price: Number(foodPrice).toFixed(2),
					categoryId: this.foodForm.categoryId,
					categoryName: this.foodForm.categoryName || '未分类',
					status: false,
					checked: false,
					checkStyle: this.inactiveCheckStyle,
					swipeOffset: 0,
					isTouching: false,
				};

				// TODO: 后续替换为 POST /foods，提交 name、price、image、categoryId
				// 如果后端使用关联表，由后端根据 categoryId 写入 food_category
				this.requestCreateFood(newFood);
				this.foodList.push(newFood);
				this.closeFoodDialog();
			},
			createLocalFoodId() {
				var maxId = 0;
				for (var i = 0; i < this.foodList.length; i++) {
					if (Number(this.foodList[i].id) > maxId) {
						maxId = Number(this.foodList[i].id);
					}
				}
				return maxId + 1;
			},
			requestCreateFood(food) {
				// TODO: 后续在这里请求后端新增菜品
			},
			handleFoodTouchStart(e, item) {
				if (!this.isEditModule) {
					return;
				}
				var touch = e.touches && e.touches[0];
				if (!touch) {
					return;
				}
				this.closeOtherFoodSwipe(item.id);
				this.touchStartX = touch.clientX;
				this.touchStartY = touch.clientY;
				this.touchStartOffset = item.swipeOffset;
				item.isTouching = true;
			},
			handleFoodTouchMove(e, item) {
				if (!this.isEditModule) {
					return;
				}
				var touch = e.touches && e.touches[0];
				if (!touch) {
					return;
				}
				var moveX = touch.clientX - this.touchStartX;
				var moveY = touch.clientY - this.touchStartY;

				if (Math.abs(moveX) < Math.abs(moveY)) {
					return;
				}

				var moveXRpx = this.pxToRpx(moveX);
				var nextOffset = this.limitFoodSwipeOffset(this.touchStartOffset + moveXRpx);
				item.swipeOffset = nextOffset;
			},
			handleFoodTouchEnd(e, item) {
				if (!this.isEditModule) {
					item.isTouching = false;
					return;
				}
				var touch = e.changedTouches && e.changedTouches[0];
				if (!touch) {
					item.isTouching = false;
					return;
				}
				var moveX = touch.clientX - this.touchStartX;
				var moveY = touch.clientY - this.touchStartY;

				item.isTouching = false;

				if (Math.abs(moveX) < Math.abs(moveY)) {
					this.setFoodSwipeOpen(item, item.swipeOffset >= this.foodDeleteOffset / 2);
					return;
				}

				if (moveX > 30 || item.swipeOffset >= this.foodDeleteOffset / 2) {
					this.setFoodSwipeOpen(item, true);
					return;
				}

				if (moveX < -30 || item.swipeOffset < this.foodDeleteOffset / 2) {
					this.setFoodSwipeOpen(item, false);
				}
			},
			handleFoodTouchCancel(item) {
				item.isTouching = false;
				this.setFoodSwipeOpen(item, item.swipeOffset >= this.foodDeleteOffset / 2);
			},
			pxToRpx(px) {
				return px * 750 / this.windowWidth;
			},
			limitFoodSwipeOffset(offset) {
				if (offset < 0) {
					return 0;
				}
				if (offset > this.foodDeleteOffset) {
					return this.foodDeleteOffset;
				}
				return offset;
			},
			setFoodSwipeOpen(item, isOpen) {
				item.isTouching = false;
				item.swipeOffset = isOpen ? this.foodDeleteOffset : 0;
			},
			closeOtherFoodSwipe(foodId) {
				for (var i = 0; i < this.foodList.length; i++) {
					if (this.foodList[i].id !== foodId) {
						this.foodList[i].isTouching = false;
						this.foodList[i].swipeOffset = 0;
					}
				}
			},
			resetFoodSwipeState() {
				for (var i = 0; i < this.foodList.length; i++) {
					this.foodList[i].isTouching = false;
					this.foodList[i].swipeOffset = 0;
				}
			},
			handleFoodDeleteTap(item) {
				this.deleteTargetFood = item;
				this.isDeleteDialogVisible = true;
			},
			cancelFoodDeleteDialog() {
				this.isDeleteDialogVisible = false;
				this.deleteTargetFood = null;
			},
			confirmFoodDelete() {
				if (!this.deleteTargetFood) {
					this.cancelFoodDeleteDialog();
					return;
				}
				var deleteFoodId = this.deleteTargetFood.id;
				this.requestDeleteFood(deleteFoodId);
				this.removeFoodById(deleteFoodId);
				this.resetFoodSwipeState();
				this.cancelFoodDeleteDialog();
			},
			removeFoodById(foodId) {
				var nextFoodList = [];
				for (var i = 0; i < this.foodList.length; i++) {
					if (this.foodList[i].id !== foodId) {
						nextFoodList.push(this.foodList[i]);
					}
				}
				this.foodList = nextFoodList;
				this.removeCheckedFood(foodId);
			},
			removeCheckedFood(foodId) {
				var checkedIndex = this.checkedFoodIds.indexOf(foodId);
				if (checkedIndex !== -1) {
					this.checkedFoodIds.splice(checkedIndex, 1);
				}
				this.syncFoodCheckedState();
			},
			requestDeleteFood(foodId) {
				// TODO: 后续在这里请求后端删除菜品
			},
			initCheckedFoodsByStatus() {
				var checkedIds = [];
				for (var i = 0; i < this.foodList.length; i++) {
					if (this.foodList[i].status) {
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
				for (var i = 0; i < this.foodList.length; i++) {
					var isChecked = this.isFoodChecked(this.foodList[i].id);
					this.foodList[i].checked = isChecked;
					this.foodList[i].checkStyle = isChecked ? this.activeCheckStyle : this.inactiveCheckStyle;
				}
			},
			toggleFoodChecked(foodId) {
				var checkedIndex = this.checkedFoodIds.indexOf(foodId);
				if (checkedIndex === -1) {
					this.checkedFoodIds.push(foodId);
				} else {
					this.checkedFoodIds.splice(checkedIndex, 1);
				}
				this.syncFoodCheckedState();
			},
			toggleSelectAll() {
				if (this.isAllFoodSelected) {
					this.checkedFoodIds = [];
					this.syncFoodCheckedState();
					return;
				}

				var checkedIds = [];
				for (var i = 0; i < this.foodList.length; i++) {
					checkedIds.push(this.foodList[i].id);
				}
				this.checkedFoodIds = checkedIds;
				this.syncFoodCheckedState();
			},
			handleSetFoodStatus(status) {
				if (!this.hasCheckedFood) {
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

				if (checkedIds.length === 0) {
					this.cancelFoodStatusDialog();
					return;
				}

				this.requestUpdateFoodStatus(checkedIds, status);

				for (var i = 0; i < this.foodList.length; i++) {
					if (checkedIds.indexOf(this.foodList[i].id) !== -1) {
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