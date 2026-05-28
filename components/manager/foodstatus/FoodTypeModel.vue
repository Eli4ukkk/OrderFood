<template>
	<!-- 分类选择 / 管理弹窗 -->
	<view v-if="visible" style="
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
			<text @tap="confirmDeleteCategory" style="font-size: 30rpx; line-height: 42rpx; color: #F55B08;">
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
					<view v-if="isCategoryChecked(item.id)" @tap.stop="toggleCategoryChecked(item)"
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
			<!-- 后端对接点：点击新增分类后，最终要调用新增菜品类型接口 -->
			<view @tap="$emit('add-type')" style="
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
</template>

<script>
	export default {
		name: 'FoodTypeModel',
		props: {
			visible: Boolean,
			categoryList: Array,
			baseUrl: String,
			token: String,
			currentCategoryId: [String, Number]
		},
		data() {
			return {
				checkedCategoryIds: [],
				selectedCategoryId: '',
				selectedCategoryName: '',
				selectedCategoryShopId: ''
			}
		},
		watch: {
			visible(value) {
				if (value) {
					this.initCategoryState();
				}
			}
		},
		computed: {
			isAllCategorySelected() {
				var list = this.categoryList || [];
				if (list.length === 0) {
					return false;
				}
				if (this.checkedCategoryIds.length !== list.length) {
					return false;
				}
				for (var i = 0; i < list.length; i++) {
					if (this.checkedCategoryIds.indexOf(list[i].id) === -1) {
						return false;
					}
				}
				return true;
			}
		},
		methods: {
			initCategoryState() {
				this.checkedCategoryIds = [];
				this.selectedCategoryId = '';
				this.selectedCategoryName = '';
				this.selectedCategoryShopId = '';

				if (!this.currentCategoryId) {
					return;
				}

				this.checkedCategoryIds = [this.currentCategoryId];

				var list = this.categoryList || [];
				for (var i = 0; i < list.length; i++) {
					if (String(list[i].id) === String(this.currentCategoryId)) {
						this.selectedCategoryId = list[i].id;
						this.selectedCategoryName = list[i].name;
						this.selectedCategoryShopId = list[i].shop_id;
						return;
					}
				}
			},
			selectCategory(item) {
				this.selectedCategoryId = item.id;
				this.selectedCategoryName = item.name;
				this.selectedCategoryShopId = item.shop_id;
				this.checkedCategoryIds = [item.id];
			},
			saveCategorySelect() {
				if (this.checkedCategoryIds.length > 1) {
					uni.showToast({
						title: '菜品只能选择一个分类',
						icon: 'none'
					});
					return;
				}

				this.$emit('select', {
					id: this.selectedCategoryId,
					name: this.selectedCategoryName,
					shop_id: this.selectedCategoryShopId
				});
				this.$emit('close');
			},
			closeCategoryDialog() {
				this.$emit('close');
			},
			isCategoryChecked(categoryId) {
				return this.checkedCategoryIds.indexOf(categoryId) !== -1;
			},
			syncSelectedCategoryByCheckedIds() {
				if (this.checkedCategoryIds.length !== 1) {
					this.selectedCategoryId = '';
					this.selectedCategoryName = '';
					this.selectedCategoryShopId = '';
					return;
				}

				var checkedId = this.checkedCategoryIds[0];
				var list = this.categoryList || [];
				for (var i = 0; i < list.length; i++) {
					if (String(list[i].id) === String(checkedId)) {
						this.selectedCategoryId = list[i].id;
						this.selectedCategoryName = list[i].name;
						this.selectedCategoryShopId = list[i].shop_id;
						return;
					}
				}
			},
			toggleCategoryChecked(item) {
				var checkedIndex = this.checkedCategoryIds.indexOf(item.id);
				if (checkedIndex === -1) {
					this.checkedCategoryIds.push(item.id);
				} else {
					this.checkedCategoryIds.splice(checkedIndex, 1);
				}
				this.syncSelectedCategoryByCheckedIds();
			},
			toggleCategorySelectAll() {
				if (this.isAllCategorySelected) {
					this.checkedCategoryIds = [];
					this.syncSelectedCategoryByCheckedIds();
					return;
				}

				var checkedIds = [];
				var list = this.categoryList || [];
				for (var i = 0; i < list.length; i++) {
					checkedIds.push(list[i].id);
				}
				this.checkedCategoryIds = checkedIds;
				this.syncSelectedCategoryByCheckedIds();
			},
			confirmDeleteCategory() {
				if (this.checkedCategoryIds.length === 0) {
					uni.showToast({
						title: '请先选择分类',
						icon: 'none'
					});
					return;
				}

				this.requestDeleteCategory(this.checkedCategoryIds.slice());
			},
			requestDeleteCategory(deleteIds) {
				// 后端对接点：删除菜品类型接口。
				// POST /api/foodd.deletetype/delete
				uni.request({
					url: this.baseUrl + '/api/foodd.deletetype/delete',
					method: 'POST',
					header: {
						token: this.token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						ids: deleteIds.join(',')
					},
					success: (res) => {
						if (Number(res.data.code) !== 1) {
							uni.showToast({
								title: res.data.msg || '删除失败',
								icon: 'none'
							});
							return;
						}

						this.checkedCategoryIds = [];
						this.syncSelectedCategoryByCheckedIds();
						this.$emit('deleted', deleteIds);

						uni.showToast({
							title: '删除成功',
							icon: 'success'
						});
					},
					fail: (err) => {
						console.log('删除分类请求失败：', err);
						uni.showToast({
							title: '请求失败',
							icon: 'none'
						});
					}
				});
			}
		}
	}
</script>
