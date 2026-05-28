<template>
	<view :style="hasSelectOpenShop ? 'padding-bottom: 170rpx;' : ''">

		<TopNav :hasBack="true" title="美尔途订餐" @back="goBack" />

		<view class="bg-white solid margin-sm padding-sm radius">
			<view class="flex justify-between">
				<view style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
					class="flex text-black align-center padding-sm justify-center radius">
					店铺管理
				</view>
				<view @click="editStone" style=" background-color: #ffaa00; height: 50rpx; width: 160rpx;"
					class="flex text-black align-center padding-sm justify-center radius">
					{{ isEditShopMode ? '完成编辑' : '编辑店铺' }}
				</view>
			</view>

			<!-- 卡片 -->
			<view v-for="item in shopList" :key="item.id" class="margin-top-sm radius"
				style="position: relative; overflow: hidden; box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1);">
				<view v-if="item.isSelectOpen" @tap.stop="handleToggleSelectShop(item)"
					class="flex align-center justify-center"
					style="position: absolute; left: 0; top: 0; bottom: 0; width: 56rpx; z-index: 1;">
					<view class="flex align-center justify-center"
						style="width: 34rpx; height: 34rpx; border: 1px solid #999999; border-radius: 50%; background-color: #ffffff;">
						<view v-if="isShopSelected(item.id)" class="bg-orange"
							style="width: 20rpx; height: 20rpx; border-radius: 50%;"></view>
					</view>
				</view>
				<view @touchstart="handleShopTouchStart($event, item)" @touchmove="handleShopTouchMove($event, item)"
					@touchend="handleShopTouchEnd($event, item)" @touchcancel="handleShopTouchCancel(item)"
					@tap="handleShopCardTap(item)" class="align-center flex padding-sm bg-white"
					:style="'position: relative; z-index: 2; transform: translateX(' + item.swipeOffset + 'rpx); transition: ' + (item.isTouching ? 'none' : 'transform 220ms ease') + ';'">
					<image class="solid" style="width: 150rpx; height: 150rpx; flex-shrink: 0;" :src="item.image"
						mode="aspectFill"></image>
					<!-- 占位 -->
					<view style="flex: 1;" class="padding-left-sm">{{ item.name }}</view>
					<view>
						<!-- 后端对接点：切换店铺营业状态，见 handleShopStatusChange。 -->
						<view @tap.stop="handleToggleShopStatus(item)" class="flex align-center"
							:style="'width: 88rpx; height: 48rpx; border-radius: 24rpx; padding: 4rpx; box-sizing: border-box; background-color: ' + (item.status ? '#0CAD17' : '#D9D9D9') + ';'">
							<view
								:style="'width: 40rpx; height: 40rpx; border-radius: 50%; background-color: #FFFFFF; transition: transform 180ms ease; transform: translateX(' + (item.status ? '40rpx' : '0') + ');'">
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>

		<view v-if="hasSelectOpenShop" class="bg-white flex align-center"
			style="box-shadow: 0 0 5px 5px rgba(0, 0, 0, 0.1); border-radius: 30px 30px 0 0; position: fixed; left: 0; right: 0; bottom: 0; z-index: 99; height: 150rpx; padding-bottom: env(safe-area-inset-bottom);">
			<!-- 后端对接点：右滑后新建店铺，提交接口见 requestCreateShop。 -->
			<view @tap="handleCreateShopTap"
				class="margin-xl round flex-sub flex align-center justify-center text-lg text-black"
				style="height: 100rpx; border: 1px solid orange;">
				新建
			</view>
			<!-- 后端对接点：右滑后删除选中店铺，提交接口见 requestDeleteShop。 -->
			<view @tap="handleDeleteShopTap" class="margin-xl round flex-sub flex align-center justify-center text-lg"
				:class="canDeleteShop ? 'text-orange' : 'text-gray'" style="height: 100rpx; border: 1px solid orange;">
				删除
			</view>
		</view>

		<view class="cu-modal" :class="showDeleteModal ? 'show' : ''">
			<view class="cu-dialog bg-white" style="border-radius: 18rpx; width: 620rpx;">
				<view style="height: 250rpx;"
					class="flex align-center justify-center padding-tb-lg text-black text-xxl text-bold">
					确定删除吗？
				</view>
				<view class="flex flex-direction align-center">
					<view @tap="handleConfirmDeleteShop" style="background-color: #D9D9D9; width: 60%;"
						class="solid flex-sub padding-tb text-gray text-lg round margin-bottom text-bold text-black">
						确定
					</view>
					<view @tap="handleCancelDeleteShop"
						style="box-sizing: border-box; border: 1px solid black; width: 60%;"
						class="flex-sub padding-tb text-gray text-lg round margin-bottom text-bold text-black">
						再想想
					</view>
				</view>
			</view>
		</view>

		<view class="cu-modal" :class="showShopFormModal ? 'show' : ''">
			<view class="cu-dialog bg-white" style="border-radius: 18rpx; width: 620rpx;">
				<view class="padding-tb text-black text-xl text-bold">
					{{ shopFormMode === 'create' ? '新建店铺' : '编辑店铺' }}
				</view>
				<view class="padding-lr-xl padding-bottom">
					<view @tap="handleChooseShopImage" class="flex align-center justify-center bg-gray radius"
						style="height: 260rpx; overflow: hidden;">
						<image v-if="shopFormImage" :src="shopFormImage" mode="aspectFill"
							style="width: 100%; height: 260rpx;"></image>
						<view v-else class="flex flex-direction align-center justify-center text-gray">
							<text class="cuIcon-cameraadd text-xxl"></text>
							<text class="margin-top-xs">上传店铺头像</text>
						</view>
					</view>
					<view @tap="handleChooseShopMenuImage"
						class="flex align-center justify-center bg-gray radius margin-top"
						style="height: 260rpx; overflow: hidden;">
						<image v-if="shopFormMenuImage" :src="shopFormMenuImage" mode="aspectFill"
							style="width: 100%; height: 260rpx;"></image>
						<view v-else class="flex flex-direction align-center justify-center text-gray">
							<text class="cuIcon-cameraadd text-xxl"></text>
							<text class="margin-top-xs">上传店铺菜单图片</text>
						</view>
					</view>
					<view class="solid margin-top radius padding-lr">
						<input :value="shopFormName" @input="handleShopFormNameInput" placeholder="请输入店铺名称"
							style="height: 90rpx; line-height: 90rpx;" />
					</view>
				</view>
				<view class="flex flex-direction align-center padding-bottom">
					<view @tap="handleConfirmShopForm" style="background-color: #D9D9D9; width: 60%;"
						class="solid flex-sub padding-tb text-gray text-lg round margin-bottom text-bold text-black">
						确定
					</view>
					<view @tap="handleCancelShopForm"
						style="box-sizing: border-box; border: 1px solid black; width: 60%;"
						class="flex-sub padding-tb text-gray text-lg round margin-bottom text-bold text-black">
						取消
					</view>
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
				/**
				 * @接口
				 */
				baseUrl: 'http://orderfood.com',
				token: '',
				uploadingShopImage: false,
				uploadingShopMenuImage: false,

				shopList: [],
				selectedShopIds: [],
				touchStartX: 0,
				touchStartY: 0,
				touchStartOffset: 0,
				ignoreNextShopTap: false,
				ignoreShopTapTimer: null,
				windowWidth: 375,
				selectOffset: 56,
				showDeleteModal: false,
				isEditShopMode: false,
				showShopFormModal: false,
				shopFormMode: 'create',
				editingShopId: null,
				shopFormName: '',
				shopFormImage: '',
				shopFormMenuImage: '',
			}
		},
		onLoad() {
			const systemInfo = uni.getSystemInfoSync()
			this.windowWidth = systemInfo.windowWidth || 375
			this.loadShopStorage()
			this.getStorageToken()
			this.requestShopList()
		},
		computed: {
			hasSelectOpenShop() {
				for (let i = 0; i < this.shopList.length; i++) {
					if (this.shopList[i].isSelectOpen) {
						return true
					}
				}
				return false
			},
			canDeleteShop() {
				return this.selectedShopIds.length > 0
			}
		},
		methods: {
			// 获取本地缓存token
			getStorageToken() {
				this.token = uni.getStorageSync('token');
			},


			handleShopTouchStart(e, item) {
				const touch = e.touches && e.touches[0]
				if (!touch) {
					return
				}
				this.touchStartX = touch.clientX
				this.touchStartY = touch.clientY
				this.touchStartOffset = item.swipeOffset
				item.isTouching = true
			},
			handleShopTouchMove(e, item) {
				const touch = e.touches && e.touches[0]
				if (!touch) {
					return
				}
				const moveX = touch.clientX - this.touchStartX
				const moveY = touch.clientY - this.touchStartY

				if (Math.abs(moveX) < Math.abs(moveY)) {
					return
				}

				const moveXRpx = this.pxToRpx(moveX)
				const nextOffset = this.limitSwipeOffset(this.touchStartOffset + moveXRpx)
				item.swipeOffset = nextOffset

				if (nextOffset > 0) {
					item.isSelectOpen = true
				}
			},
			handleShopTouchEnd(e, item) {
				const touch = e.changedTouches && e.changedTouches[0]
				if (!touch) {
					item.isTouching = false
					return
				}
				const moveX = touch.clientX - this.touchStartX
				const moveY = touch.clientY - this.touchStartY

				item.isTouching = false
				if (Math.abs(moveX) > 10 && Math.abs(moveX) > Math.abs(moveY)) {
					this.markIgnoreNextShopTap()
				}

				if (Math.abs(moveX) < Math.abs(moveY)) {
					this.setShopSelectMode(item, item.swipeOffset >= this.selectOffset / 2)
					return
				}

				if (moveX > 30 || item.swipeOffset >= this.selectOffset / 2) {
					this.setShopSelectMode(item, true)
					return
				}

				if (moveX < -30 || item.swipeOffset < this.selectOffset / 2) {
					this.setShopSelectMode(item, false)
				}
			},
			handleShopTouchCancel(item) {
				item.isTouching = false
				this.setShopSelectMode(item, item.swipeOffset >= this.selectOffset / 2)
				this.markIgnoreNextShopTap()
			},
			markIgnoreNextShopTap() {
				const that = this
				this.ignoreNextShopTap = true
				if (this.ignoreShopTapTimer) {
					clearTimeout(this.ignoreShopTapTimer)
				}
				this.ignoreShopTapTimer = setTimeout(function() {
					that.ignoreNextShopTap = false
					that.ignoreShopTapTimer = null
				}, 350)
			},
			pxToRpx(px) {
				return px * 750 / this.windowWidth
			},
			limitSwipeOffset(offset) {
				if (offset < 0) {
					return 0
				}
				if (offset > this.selectOffset) {
					return this.selectOffset
				}
				return offset
			},
			setShopSelectMode(item, isOpen) {
				item.isTouching = false
				item.isSelectOpen = isOpen
				item.swipeOffset = isOpen ? this.selectOffset : 0
				if (!isOpen) {
					this.removeSelectedShop(item.id)
				}
			},
			handleToggleSelectShop(item) {
				const index = this.selectedShopIds.indexOf(item.id)
				if (index > -1) {
					this.selectedShopIds.splice(index, 1)
					return
				}
				this.selectedShopIds.push(item.id)
			},
			removeSelectedShop(id) {
				const index = this.selectedShopIds.indexOf(id)
				if (index > -1) {
					this.selectedShopIds.splice(index, 1)
				}
			},
			isShopSelected(id) {
				return this.selectedShopIds.indexOf(id) > -1
			},
			handleCreateShopTap() {
				// 后端对接点：这里只负责打开新建弹窗，真正创建店铺在 handleConfirmShopForm -> requestCreateShop。
				this.shopFormMode = 'create'
				this.editingShopId = null
				this.shopFormName = ''
				this.shopFormImage = ''
				this.shopFormMenuImage = ''
				this.showShopFormModal = true
			},
			handleCancelShopForm() {
				this.resetShopForm()
			},
			handleShopFormNameInput(e) {
				this.shopFormName = e.detail.value
			},
			handleChooseShopImage() {
				const that = this
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['album', 'camera'],
					success: function(res) {
						if (!res.tempFilePaths || res.tempFilePaths.length === 0) {
							return
						}
						that.shopFormImage = res.tempFilePaths[0]
						that.requestUploadShopImage(that.shopFormImage)
					}
				})
			},
			handleChooseShopMenuImage() {
				const that = this
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['album', 'camera'],
					success: function(res) {
						if (!res.tempFilePaths || res.tempFilePaths.length === 0) {
							return
						}
						that.shopFormMenuImage = res.tempFilePaths[0]
						that.requestUploadShopMenuImage(that.shopFormMenuImage)
					}
				})
			},
			handleConfirmShopForm() {
				const shopName = this.shopFormName.replace(/^\s+|\s+$/g, '')
				if (!shopName) {
					uni.showToast({
						title: '请输入店铺名称',
						icon: 'none'
					})
					return
				}
				if (!this.shopFormImage) {
					uni.showToast({
						title: '请上传店铺图片',
						icon: 'none'
					})
					return
				}

				if (this.uploadingShopImage || this.uploadingShopMenuImage) {
					uni.showToast({
						title: '图片上传中，请稍等',
						icon: 'none'
					})
					return
				}

				if (this.shopFormMode === 'create') {
					const newShop = {
						id: this.getNextShopId(),
						name: shopName,
						image: this.shopFormImage,
						menuImage: this.shopFormMenuImage,
						status: false,
						swipeOffset: 0,
						isTouching: false,
						isSelectOpen: false,
					}
					this.requestCreateShop(newShop)
					return
				}
				const editShop = this.findShopById(this.editingShopId)
				if (!editShop) {
					uni.showToast({
						title: '店铺不存在',
						icon: 'none'
					})
					return
				}

				const updateShop = {
					id: editShop.id,
					name: shopName,
					image: this.shopFormImage,
					menuImage: this.shopFormMenuImage,
					status: editShop.status
				}

				this.requestUpdateShop(updateShop)
			},
			resetShopForm() {
				this.showShopFormModal = false
				this.shopFormMode = 'create'
				this.editingShopId = null
				this.shopFormName = ''
				this.shopFormImage = ''
				this.shopFormMenuImage = ''
			},
			findShopById(id) {
				for (let i = 0; i < this.shopList.length; i++) {
					if (this.shopList[i].id === id) {
						return this.shopList[i]
					}
				}
				return null
			},
			getNextShopId() {
				let maxId = 0
				for (let i = 0; i < this.shopList.length; i++) {
					if (this.shopList[i].id > maxId) {
						maxId = this.shopList[i].id
					}
				}
				return maxId + 1
			},
			requestUploadShopImage(imagePath) {
				// TODO: 后续在这里请求后端上传店铺图片，并把返回的图片地址赋值给 shopFormImage
				this.uploadingShopImage = true

				uni.uploadFile({
					url: this.baseUrl + '/api/common/upload',
					filePath: imagePath,
					method: 'POST',
					name: 'file',
					header: {
						'token': this.token
					},
					success: (res) => {
						let result = {}

						try {
							result = JSON.parse(res.data || '{}')
						} catch (err) {
							uni.showToast({
								title: '上传返回格式错误',
								icon: 'error'
							})
							return
						}

						if (result.code === 1) {
							this.shopFormImage = result.data.fullurl || result.data.url
						} else {
							uni.showToast({
								title: result.msg || '上传失败',
								icon: 'error'
							})
						}
					},
					fail: (err) => {
						console.log('上传失败', err);
						uni.showToast({
							title: '上传失败',
							icon: 'error'
						})
					},
					complete: () => {
						this.uploadingShopImage = false
					}
				})
			},
			requestUploadShopMenuImage(imagePath) {
				// TODO: 后续在这里请求后端上传店铺菜单图片，并把返回的图片地址赋值给 shopFormMenuImage
				this.uploadingShopMenuImage = true

				uni.uploadFile({
					url: this.baseUrl + '/api/common/upload',
					filePath: imagePath,
					method: 'POST',
					name: 'file',
					header: {
						'token': this.token
					},
					success: (res) => {
						let result = {}

						try {
							result = JSON.parse(res.data || '{}')
						} catch (e) {
							uni.showToast({
								title: '上传失败',
								icon: 'error'
							})
							return
						}

						if (result.code === 1) {
							this.shopFormMenuImage = result.data.fullurl || result.data.url
						} else {
							console.log('上传失败');
						}
					},
					fail: (res) => {
						console.log('上传失败');
					},
					complete: () => {
						this.uploadingShopMenuImage = false
					}
				})
			},
			requestCreateShop(shop) {
				// TODO: 后续在这里请求后端新建店铺，建议提交 name、image、menuImage、status。
				uni.request({
					url: this.baseUrl + '/api/foodd.createstone/add',
					method: 'POST',
					header: {
						token: this.token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						// id: shop.id,
						name: shop.name,
						image: shop.image,
						menuImage: shop.menuImage,
						status: shop.status ? 1 : 0
					},
					success: (res) => {
						console.log('新增店铺成功！', res.data);

						if (res.data.code === 1) {
							const newShop = {
								...res.data.data,
								swipeOffset: 0,
								isTouching: false,
								isSelectOpen: false,
							}

							this.shopList.push(newShop)
							this.syncShopStorage()
							this.resetShopForm()

							uni.showToast({
								title: '添加成功',
								icon: 'success'
							})
						} else {
							uni.showToast({
								title: res.data.msg || '添加失败',
								icon: 'none'
							})
						}
					},
					fail: (err) => {
						console.log('请求失败：', err);
					},
				})
			},
			requestUpdateShop(shop) {
				uni.request({
					url: this.baseUrl + '/api/foodd.editstone/edit',
					method: 'POST',
					header: {
						token: this.token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						id: shop.id,
						name: shop.name,
						image: shop.image,
						menuImage: shop.menuImage
					},
					success: (res) => {
						console.log('修改店铺返回：', res.data)

						if (res.data.code === 1) {
							const newShop = {
								...res.data.data,
								swipeOffset: 0,
								isTouching: false,
								isSelectOpen: false,
							}

							const index = this.shopList.findIndex(item => {
								return String(item.id) === String(newShop.id)
							})

							console.log('要替换的下标：', index)
							console.log('替换前列表：', this.shopList)
							console.log('后端返回的新店铺：', newShop)

							if (index > -1) {
								this.$set(this.shopList, index, newShop)
							}

							this.syncShopStorage()
							this.resetShopForm()

							uni.showToast({
								title: '修改成功',
								icon: 'success'
							})
						} else {
							uni.showToast({
								title: res.data.msg || '修改失败',
								icon: 'none'
							})
						}
					},
					fail: (err) => {
						console.log('请求失败：', err)

						uni.showToast({
							title: '请求失败',
							icon: 'none'
						})
					}
				})
			},
			handleDeleteShopTap() {
				// 后端对接点：这里只负责打开删除确认框，真正删除在 handleConfirmDeleteShop -> requestDeleteShop。
				if (!this.canDeleteShop) {
					return
				}
				this.showDeleteModal = true
			},
			handleCancelDeleteShop() {
				this.showDeleteModal = false
			},
			handleConfirmDeleteShop() {
				const deleteIds = this.selectedShopIds.slice()
				if (deleteIds.length === 0) {
					this.showDeleteModal = false
					return
				}
				// 后端对接点
				this.requestDeleteShop(deleteIds)
			},
			requestDeleteShop(ids) {
				// TODO: 后续在这里请求后端删除店铺，建议提交 ids 数组，后端批量删除。
				// POST /api/foodd.deletestone/delete
				// header: {
				//   token: this.token,
				//   'content-type': 'application/x-www-form-urlencoded'
				// }
				// data: {
				//   ids: ids
				// }
				uni.request({
					url: this.baseUrl + '/api/foodd.deletestone/delete',
					method: 'POST',
					header: {
						token: this.token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						'ids': ids
					},
					success: (res) => {
						if (res.data.code === 1) {
							this.selectedShopIds = []
							this.showDeleteModal = false
				
							this.requestShopList()
				
							uni.showToast({
								title: '删除成功',
								icon: 'success'
							})
						} else {
							console.log('请求失败，res.data：', res.data);
						}
					},
					fail: (res) => {
						uni.showToast({
							title: '删除失败',
							icon: 'error'
						})
					}
					
				})

				const deleteIdMap = {}
				ids.forEach(id => {
					deleteIdMap[String(id)] = true
				})
				this.shopList = this.shopList.filter(item => {
					return !deleteIdMap[String(item.id)]
				})
				this.selectedShopIds = []
				this.showDeleteModal = false
				this.syncShopStorage()

				uni.showToast({
					title: '删除成功',
					icon: 'success'
				})
			},
			requestShopList() {
				uni.request({
					url: this.baseUrl + '/api/foodd.searchallstone/searchall',
					method: 'GET',
					header: {
						token: this.token
					},
					success: (res) => {
						if (res.data.code === 1) {
							const list = res.data.data.list || []

							this.shopList = list.map(item => {
								return {
									id: item.id,
									name: item.name,
									image: item.avatar,
									menuImage: item.menu_image || '',
									status: item.status === 'normal',

									// 前端页面状态，后端不需要存
									swipeOffset: 0,
									isTouching: false,
									isSelectOpen: false
								}
							})
						}
					}
				})
			},
			handleToggleShopStatus(item) {
				const nextStatus = !item.status

				uni.request({
					url: this.baseUrl + '/api/foodd.togglestone/toggle',
					method: 'POST',
					header: {
						token: this.token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						id: item.id,
						status: nextStatus ? 1 : 0
					},
					success: (res) => {
						console.log('切换店铺状态返回：', res.data)

						if (res.data.code === 1) {
							item.status = !!res.data.data.status
							this.syncShopStorage()

							uni.showToast({
								title: '修改成功',
								icon: 'success'
							})
						} else {
							uni.showToast({
								title: res.data.msg || '修改店铺状态失败',
								icon: 'none'
							})
						}
					},
					fail: (err) => {
						console.log('请求修改店铺状态失败', err)

						uni.showToast({
							title: '修改店铺状态失败',
							icon: 'none'
						})
					}
				})
			},
			// 这个函数已经没用了
			handleShopStatusChange(item) {
				// TODO: 后续在这里请求后端修改店铺营业状态，建议提交 id、status。
			},
			loadShopStorage() {
				// TODO: 后续店铺数据改为后端维护，这里替换为 GET /shops
				const localShopList = uni.getStorageSync('shopList') || []
				if (!Array.isArray(localShopList) || localShopList.length === 0) {
					this.syncShopStorage()
					return
				}
				const nextShopList = []
				for (let i = 0; i < localShopList.length; i++) {
					nextShopList.push({
						id: localShopList[i].id,
						name: localShopList[i].name,
						image: localShopList[i].image,
						menuImage: localShopList[i].menuImage || '',
						status: !!localShopList[i].status,
						swipeOffset: 0,
						isTouching: false,
						isSelectOpen: false,
					})
				}
				this.shopList = nextShopList
			},
			syncShopStorage() {
				// TODO: 后续店铺数据改为后端维护，这里删除本地缓存同步逻辑
				const shopListWithoutPageState = []
				for (let i = 0; i < this.shopList.length; i++) {
					shopListWithoutPageState.push({
						id: this.shopList[i].id,
						name: this.shopList[i].name,
						image: this.shopList[i].image,
						menuImage: this.shopList[i].menuImage || '',
						status: this.shopList[i].status,
					})
				}
				uni.setStorageSync('shopList', shopListWithoutPageState)
			},
			goBack() {
				const pages = getCurrentPages()
				if (pages.length > 1) {
					uni.navigateBack()
					return
				}
				uni.navigateTo({
					url: '/pages/index/manager/3'
				})
			},

			editStone() {
				this.isEditShopMode = !this.isEditShopMode
				if (this.isEditShopMode) {
					uni.showToast({
						icon: 'none',
						title: '点击店铺卡片以编辑',
						duration: 1000
					})
				} else {
					// 后端对接点：点击完成编辑。目前编辑店铺在弹窗确认时已提交，这里一般只退出编辑模式。
					uni.showToast({
						title: '完成编辑！',
						duration: 1000
					})
				}
			},
			handleShopCardTap(item) {
				if (this.ignoreNextShopTap) {
					this.ignoreNextShopTap = false
					if (this.ignoreShopTapTimer) {
						clearTimeout(this.ignoreShopTapTimer)
						this.ignoreShopTapTimer = null
					}
					return
				}
				if (!this.isEditShopMode) {
					return
				}
				if (item.isSelectOpen) {
					return
				}
				this.openEditShopModal(item)
			},
			openEditShopModal(item) {
				this.shopFormMode = 'edit'
				this.editingShopId = item.id
				this.shopFormName = item.name
				this.shopFormImage = item.image
				this.shopFormMenuImage = item.menuImage || ''
				this.showShopFormModal = true
			}
		}
	}
</script>
