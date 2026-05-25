<template>
	<view class="container bg-white">
		<!-- 顶部 -->
		<TopNav title="美尔途订餐" :hasBack="false"></TopNav>
		
		<view class="order-header">
			<!-- 背景图 -->
			<BackGoundImage
				:image="backgroundImage">
			</BackGoundImage>

			<!-- 下拉看菜单 -->
			<PullToLookMenu
				:bigImage="bigImage"
				:preImage="preImage">
			</PullToLookMenu>
		</view>
		
		<!-- 点餐 + 全部 + 搜索-->
		<OrderAllSearch 
			:categoryType="categoryType">
		</OrderAllSearch>

		<view class="order-main">
			<!-- 左侧分类栏 -->
			<LeftCategoryBar
				:categoryList="categoryList"
				@categoryChange="handlecategoryChange"
				:categoryIndex="categoryIndex">
			</LeftCategoryBar>

			<!-- 中间展示菜品 -->
			<view class="order-food-area">
				<CenterFoodDisplay
					class="order-food-display"
					style="width: 100%; height: 100%; display: block;"
					:foodList="foodList"
					:backendType="categoryType"
					@addItem="handleAddItem"
					@subItem="handleSubItem">
				</CenterFoodDisplay>
			</view>
		</view>
		


		<!-- 下方总价格（固定在底部） -->
		<!-- 购物车内容弹窗 -->
		<BottomTotalPrice
			@toggleCart="handleToggleCart"
			:cart="cart"
			:openCart="openCart"
			:totalPrice="totalPrice">
			
			
			<template #slotOne>
			    <view class="">
					<SlotOne 
					@updateRemarkValue="handleUpdateRemarkValue"
					@clearCart="handleClearCart"
					@addItem="handleAddItem"
					@subItem="handleSubItem"
					:cart="cart"
					:remark="remark"/>
				</view>
			</template>
		</BottomTotalPrice>
	</view>
</template>

<script>
	/**
	 * @注册组件
	 */
	import BackGoundImage from 'components/2/BackGoundImage.vue' // 背景图组件
	import PullToLookMenu from 'components/2/PullToLookMenu.vue' // 下拉看菜单组件
	import OrderAllSearch from 'components/2/OrderAllSearch.vue' // 点餐 + 全部 + 搜索组件
	import LeftCategoryBar from 'components/2/LeftCategoryBar.vue' // 左侧分类栏组件
	import CenterFoodDisplay from 'components/2/CenterFoodDisplay.vue' // 中间菜品展示组件
	import BottomTotalPrice from 'components/2/BottomTotalPrice.vue' // 下方总价格组件
	import SlotOne from 'components/2/SlotOne.vue'					 // slotOne组件
	import TopNav from 'components/common/TopNav.vue'

	export default {
		components: {
			/**
			 * @声明组件
			 */
			BackGoundImage,
			PullToLookMenu,
			OrderAllSearch,
			LeftCategoryBar,
			CenterFoodDisplay,
			BottomTotalPrice,
			SlotOne,
			TopNav
		},
		data() {
			return {
				/**
				 * @数据
				 */
				backgroundImage: '/static/2/Mask group.png', 	// 背景图路径
				
				preImage: '/static/2/Rectangle 405.png',		 // 下拉看菜单预览图路径
				bigImage: '/static/2/Rectangle 437.png', 		// 下拉看菜单图片路径
				
				categoryList: [
					{
						id: 'all',
						name: '全部',
					}
				], // “全部”只存在前端，不存入本地缓存
				categoryId: 'all',							// 分类 id
				categoryType: '全部',							// 分类类型
				categoryIndex: 0, 								// 分类索引
				
				foodList: [
					{ 									// 菜品列表
						image: "/static/2/foot.png",
						title: "烧排骨",
						price: "8.00",
						number: 1,
					}, 
				],
				
				totalPrice: '0.00', 						// 总价(后端计算总价)
				cart: [
					{									// 购物车列表
						image: "/static/2/foot.png",
						title: "烧排骨",
						price: "8.00",
						number: 1,
					},
				],
				remark: '',									// 备注内容
				openCart: false,								// 购物车弹窗状态
			}
		},
		created() {
			this.updateTotalPrice();
		},
		onShow() {
			this.loadCategoryList();
		},
		methods: {
			/**
			 * @组件通信方法
			 */
			handlecategoryChange({index, id, type}) {
				this.categoryIndex = index;
				if(id || id === 0) {
					this.categoryId = id;
				}
				else {
					this.categoryId = '';
				}
				if(type && type.name) {
					this.categoryType = type.name;
				}
				else {
					this.categoryType = type;
				}
				// TODO: 后续点击分类后请求 GET /foods?categoryId=xxx；id 为 all 时请求 GET /foods
			},
			loadCategoryList() {
				// TODO: 后续接后端时，这里替换为 GET /categories
				var localCategoryList = uni.getStorageSync('categoryList') || [];
				var nextCategoryList = [
					{
						id: 'all',
						name: '全部',
					}
				];

				for(var i = 0; i < localCategoryList.length; i++) {
					if(localCategoryList[i] && localCategoryList[i].id !== 'all') {
						nextCategoryList.push({
							id: localCategoryList[i].id,
							name: localCategoryList[i].name,
						});
					}
				}

				this.categoryList = nextCategoryList;
				if(this.categoryIndex >= this.categoryList.length) {
					this.categoryIndex = 0;
					this.categoryId = 'all';
					this.categoryType = '全部';
				}
			},
			handleToggleCart(status) {
				this.openCart = status;
			},
			handleUpdateRemarkValue(remark_) {
				this.remark = remark_;
			},
			handleClearCart() {
				this.cart = [];
				this.totalPrice = '0.00';
				this.remark = '';
				this.openCart = false;
				this.syncFoodNumbers();
			},
			handleAddItem(payload) {
				const item = payload && payload.item;
				this.updateItemNumber(item, 1);
			},
			handleSubItem(payload) {
				const item = payload && payload.item;
				this.updateItemNumber(item, -1);
			},
			updateItemNumber(item, step) {
				if (!item) return;
				
				const title = item.title;
				const sameReferenceIndex = this.foodList.indexOf(item);
				const foodIndex = sameReferenceIndex !== -1
					? sameReferenceIndex
					: this.foodList.findIndex(food => food.title === title);
				if (foodIndex === -1) return;
				
				const currentNumber = Number(this.foodList[foodIndex].number || 0);
				const nextNumber = Math.max(0, currentNumber + step);
				
				this.$set(this.foodList[foodIndex], 'number', nextNumber);
				this.syncCartFromFood(this.foodList[foodIndex]);
				this.updateTotalPrice();
			},
			syncCartFromFood(food) {
				const cartIndex = this.cart.findIndex(item => item.title === food.title);
				
				if (food.number > 0) {
					const cartItem = {
						image: food.image,
						title: food.title,
						price: food.price,
						number: food.number,
					};
					
					if (cartIndex === -1) {
						this.cart.push(cartItem);
					} else {
						this.$set(this.cart, cartIndex, cartItem);
					}
				} else if (cartIndex !== -1) {
					this.cart.splice(cartIndex, 1);
				}
				
				if (this.cart.length === 0) {
					this.openCart = false;
				}
			},
			syncFoodNumbers() {
				this.foodList.forEach((food, index) => {
					this.$set(this.foodList[index], 'number', 0);
				});
			},
			updateTotalPrice() {
				const total = this.cart.reduce((sum, item) => {
					return sum + Number(item.price || 0) * Number(item.number || 0);
				}, 0);
				
				this.totalPrice = total.toFixed(2);
			}
		}
	}
</script>

<style>
	.container {
		height: 100vh;
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		padding-bottom: calc(130rpx + var(--window-bottom, 0px));
		overflow: hidden;
	}

	.order-header {
		position: relative;
		height: 330rpx;
		flex-shrink: 0;
		overflow: visible;
		z-index: 30;
	}

	.order-main {
		position: relative;
		flex: 1;
		min-height: 0;
		display: flex;
		overflow: hidden;
		z-index: 1;
	}

	.order-food-area {
		flex: 1;
		width: 0;
		min-width: 0;
		height: 100%;
		display: flex;
		overflow: hidden;
	}

	.order-food-display {
		width: 100%;
		height: 100%;
		display: block;
	}
</style>
