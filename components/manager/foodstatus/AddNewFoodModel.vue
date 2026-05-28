<template>
	<view>
		<!-- 新增菜品弹窗 -->
		<view v-if="visible" @tap="closeFoodDialog" style="
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
						<!-- 后端对接点：新增菜品类型后，这里的分类名称需要来自后端分类列表 -->
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

		<FoodTypeModel :visible="showCategoryDialog" :category-list="categoryList" :base-url="baseUrl"
			:token="token" :current-category-id="foodForm.categoryId" @select="selectCategory"
			@deleted="handleCategoryDeleted" @add-type="$emit('add-type')"
			@close="showCategoryDialog = false" />
	</view>
</template>

<script>
	import FoodTypeModel from "@/components/manager/foodstatus/FoodTypeModel.vue"

	export default {
		name: 'AddNewFoodModel',
		components: {
			FoodTypeModel
		},
		props: {
			visible: Boolean,
			baseUrl: String,
			token: String,
			categoryList: Array
		},
		data() {
			return {
				showCategoryDialog: false,
				uploadingFoodImage: false,
				foodImageUploadToken: 0,
				creatingFood: false,
				foodForm: {
					image: '',
					name: '',
					categoryId: '',
					categoryName: '未分类',
					stoneId: '',
					price: ''
				}
			}
		},
		methods: {
			resetFoodForm() {
				this.foodForm = {
					image: '',
					name: '',
					categoryId: '',
					categoryName: '未分类',
					stoneId: '',
					price: ''
				};
				this.uploadingFoodImage = false;
				this.foodImageUploadToken += 1;
			},
			closeFoodDialog() {
				this.resetFoodForm();
				this.$emit('close');
			},
			chooseFoodImage() {
				if (this.uploadingFoodImage) {
					uni.showToast({
						title: '图片上传中，请稍等',
						icon: 'none'
					});
					return;
				}

				var self = this;
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['album', 'camera'],
					success: function(res) {
						if (res.tempFilePaths && res.tempFilePaths.length > 0) {
							var imagePath = res.tempFilePaths[0];
							self.foodForm.image = imagePath;
							self.requestUploadFoodImage(imagePath);
						}
					}
				});
			},
			isTempFoodImagePath(imagePath) {
				if (!imagePath) {
					return false;
				}
				return imagePath.indexOf('wxfile://') === 0 ||
					imagePath.indexOf('/tmp/') === 0 ||
					imagePath.indexOf('http://tmp/') === 0 ||
					imagePath.indexOf('https://tmp/') === 0;
			},
			requestUploadFoodImage(imagePath) {
				// TODO: 后续在这里请求后端上传菜品图片，并把返回的正式图片地址赋值给 foodForm.image
				// POST /api/common/upload
				this.foodImageUploadToken += 1;
				var uploadToken = this.foodImageUploadToken;
				this.uploadingFoodImage = true;

				uni.uploadFile({
					url: this.baseUrl + '/api/common/upload',
					filePath: imagePath,
					method: 'POST',
					name: 'file',
					header: {
						token: this.token
					},
					success: (res) => {
						var result = {};

						try {
							result = JSON.parse(res.data || '{}');
						} catch (err) {
							uni.showToast({
								title: '上传返回格式错误',
								icon: 'none'
							});
							return;
						}

						if (this.foodImageUploadToken !== uploadToken || this.foodForm.image !== imagePath) {
							return;
						}

						if (result.code === 1) {
							var imageUrl = result.data && (result.data.fullurl || result.data.url);
							if (!imageUrl) {
								uni.showToast({
									title: '上传返回缺少图片地址',
									icon: 'none'
								});
								return;
							}
							this.foodForm.image = imageUrl;
							return;
						}

						uni.showToast({
							title: result.msg || '上传失败',
							icon: 'none'
						});
					},
					fail: (err) => {
						console.log('菜品图片上传失败：', err);
						uni.showToast({
							title: '上传失败',
							icon: 'none'
						});
					},
					complete: () => {
						if (this.foodImageUploadToken === uploadToken) {
							this.uploadingFoodImage = false;
						}
					}
				});
			},
			openCategoryDialog() {
				// 后端对接点：打开分类管理前，可由父页面调用查询菜品类型接口刷新 categoryList。
				this.showCategoryDialog = true;
			},
			selectCategory(item) {
				this.foodForm.categoryId = item.id;
				this.foodForm.categoryName = item.name || '未分类';
				this.foodForm.stoneId = item.shop_id;
			},
			handleCategoryDeleted(deleteIds) {
				if (deleteIds.indexOf(this.foodForm.categoryId) !== -1) {
					this.foodForm.categoryId = '';
					this.foodForm.categoryName = '未分类';
					this.foodForm.stoneId = '';
				}

				this.$emit('type-deleted', deleteIds);
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
				if (isNaN(Number(foodPrice)) || Number(foodPrice) <= 0) {
					uni.showToast({
						title: '请输入正确价格',
						icon: 'none'
					});
					return;
				}
				if (!this.foodForm.categoryId || !this.foodForm.stoneId) {
					uni.showToast({
						title: '请选择菜品分类',
						icon: 'none'
					});
					return;
				}
				if (this.uploadingFoodImage) {
					uni.showToast({
						title: '图片上传中，请稍等',
						icon: 'none'
					});
					return;
				}
				if (this.isTempFoodImagePath(this.foodForm.image)) {
					uni.showToast({
						title: '请等待图片上传完成',
						icon: 'none'
					});
					return;
				}

				var newFood = {
					image: this.foodForm.image || '/static/logo.png',
					name: foodName,
					price: Number(foodPrice).toFixed(2),
					categoryId: this.foodForm.categoryId,
					categoryName: this.foodForm.categoryName || '未分类',
					stoneId: this.foodForm.stoneId
				};

				// 后端对接点：新增菜品确认入口，校验通过后调用 requestCreateFood。
				// TODO: 后续替换为 POST /foods，提交 name、price、image、type_id、stone_id
				this.requestCreateFood(newFood);
			},
			requestCreateFood(food) {
				// TODO: 后续在这里请求后端新增菜品
				// POST /foods
				// body: {
				//   name: food.name,
				//   price: food.price,
				//   image: food.image,
				//   type_id: food.categoryId,
				//   stone_id: food.stoneId
				// }
				if (this.creatingFood) {
					return;
				}
				this.creatingFood = true;

				uni.request({
					url: this.baseUrl + '/api/foodd.createfood/create',
					method: 'POST',
					header: {
						token: this.token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						name: food.name,
						price: food.price,
						image: food.image,
						type_id: food.categoryId,
						stone_id: food.stoneId
					},
					success: (res) => {
						if (res.data.code !== 1) {
							uni.showToast({
								title: res.data.msg || '添加失败',
								icon: 'none'
							});
							return;
						}

						this.$emit('created');
						this.closeFoodDialog();

						uni.showToast({
							title: '添加成功',
							icon: 'success'
						});
					},
					fail: (err) => {
						console.log('请求失败：', err);
						uni.showToast({
							title: '添加失败',
							icon: 'none'
						});
					},
					complete: () => {
						this.creatingFood = false;
					}
				});
			}
		}
	}
</script>
