<template>
	<!-- 新增分类小弹窗 -->
	<view class="">
		<view v-if="visible" @tap="closeAddCategoryDialog" style="
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 1001;
		background-color: rgba(0, 0, 0, 0.18);" class="flex align-center justify-center">
			<view @tap.stop style="
			width: 660rpx;
			min-height: 420rpx;
			background-color: #FFFFFF;
			border-radius: 18rpx;
			box-sizing: border-box;
			padding: 28rpx 30rpx 24rpx 30rpx;" class="flex flex-direction">
				<input v-model="newCategoryName" placeholder="请输入新菜品分类..."
					placeholder-style="color: #111111; font-weight: bold;" style="
				width: 100%;
				height: 200rpx;
				line-height: 72rpx;
				font-size: 30rpx;
				color: #111111;
				font-weight: bold;" />
		
				<view @tap="openShopDialog" style="
				width: 100%;
				height: 72rpx;
				margin-top: 26rpx;
				padding: 0 20rpx;
				background-color: #D9D9D9;
				border-radius: 12rpx;
				box-sizing: border-box;" class="flex align-center justify-between">
					<text style="font-size: 30rpx; color: #111111;">选择店铺</text>
					<view class="flex align-center">
						<text style="font-size: 28rpx; color: #555555;">
							{{selectedShopName || '请选择店铺'}}
						</text>
						<text style="font-size: 38rpx; color: #333333; margin-left: 12rpx;"
							class="cuIcon-right"></text>
					</view>
				</view>
		
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
					<!-- 后端对接点：完成按钮触发 confirmAddCategory，需要把新菜品类型提交给后端 -->
					完成
				</view>
			</view>
		</view>
		
		<ChooseStoneModel :visible="showShopDialog" :base-url="baseUrl" :token="token"
			:selected-shop-id="selectedShopId" @select="handleShopSelect" @close="showShopDialog = false" />
	</view>
</template>

<script>
	import ChooseStoneModel from "@/components/manager/foodstatus/ChooseStoneModel.vue"

	export default {
		name: 'AddNewTypeModel',
		components: {
			ChooseStoneModel
		},
		props: {
			visible: Boolean,
			baseUrl: String,
			token: String
		},
		data() {
			return {
				newCategoryName: '',
				showShopDialog: false,
				selectedShopId: '',
				selectedShopName: '',
				creatingType: false
			}
		},
		methods: {
			openShopDialog() {
				this.showShopDialog = true;
			},
			handleShopSelect(item) {
				this.selectedShopId = item.id;
				this.selectedShopName = item.name;
				this.showShopDialog = false;
			},
			resetAddCategoryForm() {
				this.newCategoryName = '';
				this.showShopDialog = false;
				this.selectedShopId = '';
				this.selectedShopName = '';
				this.creatingType = false;
			},
			closeAddCategoryDialog() {
				this.resetAddCategoryForm();
				this.$emit('close');
			},
			confirmAddCategory() {
				var categoryName = this.newCategoryName ? this.newCategoryName.trim() : '';
				if (!categoryName) {
					uni.showToast({
						title: '请输入菜品分类',
						icon: 'none'
					});
					return;
				}
				if (!this.selectedShopId) {
					uni.showToast({
						title: '请选择店铺',
						icon: 'none'
					});
					return;
				}
				if (this.creatingType) {
					return;
				}

				this.creatingType = true;

				// 后端对接点：新增菜品类型提交给后端。
				// TODO: 后续可替换为正式分类接口
				// POST /categories
				// body: {
				//   name: categoryName,
				//   shopId: this.selectedShopId,
				// }
				uni.request({
					url: this.baseUrl + '/api/foodd.createtype/create',
					method: 'POST',
					header: {
						token: this.token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						newtype: categoryName,
						shop_id: this.selectedShopId
					},
					success: (res) => {
						console.log('新增分类返回：', res.data);

						if (Number(res.data.code) === 1) {
							this.$emit('created');
							this.closeAddCategoryDialog();

							uni.showToast({
								title: '添加成功',
								icon: 'success'
							});
						} else {
							uni.showToast({
								title: res.data.msg || '添加失败',
								icon: 'none'
							});
						}
					},
					fail: (err) => {
						console.log('新增分类请求失败：', err);
						uni.showToast({
							title: '请求失败',
							icon: 'none'
						});
					},
					complete: () => {
						this.creatingType = false;
					}
				});
			}
		}
	}
</script>
