<template>
	<view :style="hasSelectOpenShop ? 'padding-bottom: 170rpx;' : ''">
		
		<TopNav
		:hasBack="true"
		title="美尔途订餐"
		@back="goBack"
		/>
		
		<view class="bg-white solid margin-sm padding-sm radius">
			<view
			style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
			class="flex text-black align-center padding-sm justify-center radius">
				店铺管理
			</view>
			<!-- 卡片 -->
			<view
			v-for="item in shopList"
			:key="item.id"
			class="margin-top-sm radius"
			style="position: relative; overflow: hidden; box-shadow: 0 0 2px 2px rgba(0, 0, 0, 0.1);">
				<view
				v-if="item.isSelectOpen"
				@tap.stop="handleToggleSelectShop(item)"
				class="flex align-center justify-center"
				style="position: absolute; left: 0; top: 0; bottom: 0; width: 56rpx; z-index: 1;">
					<view
					class="flex align-center justify-center"
					style="width: 34rpx; height: 34rpx; border: 1px solid #999999; border-radius: 50%; background-color: #ffffff;">
						<view
						v-if="isShopSelected(item.id)"
						class="bg-orange"
						style="width: 20rpx; height: 20rpx; border-radius: 50%;"></view>
					</view>
				</view>
				<view
				@touchstart="handleShopTouchStart($event, item)"
				@touchmove="handleShopTouchMove($event, item)"
				@touchend="handleShopTouchEnd($event, item)"
				@touchcancel="handleShopTouchCancel(item)"
				class="align-center flex padding-sm bg-white"
				:style="'position: relative; z-index: 2; transform: translateX(' + item.swipeOffset + 'rpx); transition: ' + (item.isTouching ? 'none' : 'transform 220ms ease') + ';'">
					<image 
					class="solid"
					style="width: 150rpx; height: 150rpx; flex-shrink: 0;"
					:src="item.image" mode="aspectFill"></image>
					<!-- 占位 -->
					<view 
					style="flex: 1;"
					class="padding-left-sm">{{ item.name }}</view>
					<view>
						<view
						@tap.stop="handleToggleShopStatus(item)"
						class="flex align-center"
						:style="'width: 88rpx; height: 48rpx; border-radius: 24rpx; padding: 4rpx; box-sizing: border-box; background-color: ' + (item.status ? '#0CAD17' : '#D9D9D9') + ';'">
							<view
							:style="'width: 40rpx; height: 40rpx; border-radius: 50%; background-color: #FFFFFF; transition: transform 180ms ease; transform: translateX(' + (item.status ? '40rpx' : '0') + ');'"></view>
						</view>
					</view>
				</view>
			</view>
		</view>

		<view
		v-if="hasSelectOpenShop"
		class="bg-white flex align-center"
		style="box-shadow: 0 0 5px 5px rgba(0, 0, 0, 0.1); border-radius: 30px 30px 0 0; position: fixed; left: 0; right: 0; bottom: 0; z-index: 99; height: 150rpx; padding-bottom: env(safe-area-inset-bottom);">
			<view
			@tap="handleCreateShopTap"
			class="margin-xl round flex-sub flex align-center justify-center text-lg text-black"
			style="height: 100rpx; border: 1px solid orange;">
				新建
			</view>
			<view
			@tap="handleDeleteShopTap"
			class="margin-xl round flex-sub flex align-center justify-center text-lg"
			:class="canDeleteShop ? 'text-orange' : 'text-gray'"
			style="height: 100rpx; border: 1px solid orange;">
				删除
			</view>
		</view>

		<view
		class="cu-modal"
		:class="showDeleteModal ? 'show' : ''">
			<view
			class="cu-dialog bg-white"
			style="border-radius: 18rpx; width: 620rpx;">
				<view 
				style="height: 250rpx;"
				class="flex align-center justify-center padding-tb-lg text-black text-xxl text-bold">
					确定删除吗？
				</view>
				<view class="flex flex-direction align-center">
					<view
					@tap="handleConfirmDeleteShop"
					style="background-color: #D9D9D9; width: 60%;"
					class="solid flex-sub padding-tb text-gray text-lg round margin-bottom text-bold text-black">
						确定
					</view>
					<view
					@tap="handleCancelDeleteShop"
					style="box-sizing: border-box; border: 1px solid black; width: 60%;"
					class="flex-sub padding-tb text-gray text-lg round margin-bottom text-bold text-black">
						再想想
					</view>
				</view>
			</view>
		</view>

		<view
		class="cu-modal"
		:class="showCreateModal ? 'show' : ''">
			<view
			class="cu-dialog bg-white"
			style="border-radius: 18rpx; width: 620rpx;">
				<view class="padding-tb text-black text-xl text-bold">
					新建店铺
				</view>
				<view class="padding-lr-xl padding-bottom">
					<view
					@tap="handleChooseShopImage"
					class="flex align-center justify-center bg-gray radius"
					style="height: 260rpx; overflow: hidden;">
						<image
						v-if="createShopImage"
						:src="createShopImage"
						mode="aspectFill"
						style="width: 100%; height: 260rpx;"></image>
						<view
						v-else
						class="flex flex-direction align-center justify-center text-gray">
							<text class="cuIcon-cameraadd text-xxl"></text>
							<text class="margin-top-xs">上传店铺图片</text>
						</view>
					</view>
					<view class="solid margin-top radius padding-lr">
						<input
						:value="createShopName"
						@input="handleCreateShopNameInput"
						placeholder="请输入店铺名称"
						style="height: 90rpx; line-height: 90rpx;" />
					</view>
				</view>
				<view class="flex flex-direction align-center padding-bottom">
					<view
					@tap="handleConfirmCreateShop"
					style="background-color: #D9D9D9; width: 60%;"
					class="solid flex-sub padding-tb text-gray text-lg round margin-bottom text-bold text-black">
						确定
					</view>
					<view
					@tap="handleCancelCreateShop"
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
				shopList: [
					{
						id: 1,
						name: '优客来',
						image: '/static/logo.png',
						status: true,
						swipeOffset: 0,
						isTouching: false,
						isSelectOpen: false,
					},
					{
						id: 2,
						name: '美尔途',
						image: '/static/logo.png',
						status: true,
						swipeOffset: 0,
						isTouching: false,
						isSelectOpen: false,
					}
				],
				selectedShopIds: [],
				touchStartX: 0,
				touchStartY: 0,
				touchStartOffset: 0,
				windowWidth: 375,
				selectOffset: 56,
				showDeleteModal: false,
				showCreateModal: false,
				createShopName: '',
				createShopImage: '',
			}
		},
		onLoad() {
			const systemInfo = uni.getSystemInfoSync()
			this.windowWidth = systemInfo.windowWidth || 375
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
				this.createShopName = ''
				this.createShopImage = ''
				this.showCreateModal = true
			},
			handleCancelCreateShop() {
				this.showCreateModal = false
			},
			handleCreateShopNameInput(e) {
				this.createShopName = e.detail.value
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
						that.createShopImage = res.tempFilePaths[0]
						that.requestUploadShopImage(that.createShopImage)
					}
				})
			},
			handleConfirmCreateShop() {
				const shopName = this.createShopName.replace(/^\s+|\s+$/g, '')
				if (!shopName) {
					uni.showToast({
						title: '请输入店铺名称',
						icon: 'none'
					})
					return
				}
				if (!this.createShopImage) {
					uni.showToast({
						title: '请上传店铺图片',
						icon: 'none'
					})
					return
				}
				const newShop = {
					id: this.getNextShopId(),
					name: shopName,
					image: this.createShopImage,
					status: true,
					swipeOffset: 0,
					isTouching: false,
					isSelectOpen: false,
				}
				this.requestCreateShop(newShop)
				this.shopList.push(newShop)
				this.createShopName = ''
				this.createShopImage = ''
				this.showCreateModal = false
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
				// TODO: 后续在这里请求后端上传店铺图片，并把返回的图片地址赋值给 createShopImage
			},
			requestCreateShop(shop) {
				// TODO: 后续在这里请求后端新建店铺
			},
			handleDeleteShopTap() {
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
				this.requestDeleteShop(deleteIds)
				const nextShopList = []
				for (let i = 0; i < this.shopList.length; i++) {
					if (deleteIds.indexOf(this.shopList[i].id) === -1) {
						nextShopList.push(this.shopList[i])
					}
				}
				this.shopList = nextShopList
				this.selectedShopIds = []
				this.showDeleteModal = false
			},
			requestDeleteShop(ids) {
				// TODO: 后续在这里请求后端删除店铺
			},
			handleToggleShopStatus(item) {
				item.status = !item.status
				this.handleShopStatusChange(item)
			},
			handleShopStatusChange(item) {
				// TODO: 后续在这里请求后端修改店铺营业状态
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
			}
		}
	}
</script>
