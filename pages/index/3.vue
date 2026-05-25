<template>
	<view class="margin-bottom-xl">
		<TopNav :hasBack="false" title="美尔途订餐" />
		
		<Card 
		:info="userInfo"
		:clickImageFunction="true"
		@clickImage="clickLogin = true"
		>
			<template #login>
				<LoginSlot
				:userInfo="userInfo"
				@saveChange="saveUserInfo"
				@clickQuit="clickLogin = false"
				@openProtocol="handleopenProtocol"
				:clickLogin="clickLogin"
				>
				
					<template #server>
						<Server
						@back="goBack"
						:isShow="isShowServer"
						:content="serverContent"
						></Server>
					</template>
					<template #private>
						<Private
						@back="goBack"
						:isShow="isShowPrivate"
						:content="privateContent"
						></Private>
					</template>
					
				</LoginSlot>
			</template>
		</Card>
		
		<!-- 管理员 -->
		<view v-if="isManager">
			<view class="manager-row">
				<view
				@tap="goUnorderPage"
				class="manager-cell manager-cell-left">
					<ManagerCard :info="leftinfo"></ManagerCard>
				</view>
		
				<view
				@tap="goOrderPricePage"
				class="manager-cell">
					<ManagerCard :info="rightinfo"></ManagerCard>
				</view>
			</view>
		
			<Card
			:info="personOrderHistory"
			:clickImageFunction="false"
			:clickCardFunction="true"
			@clickCard="handleclickCard"
			name="managerFoodInfo"
			>
				<template #managerFoodInfo>
					<FoodInfo
					:isPull="isPull"
					:foodHistory="foodHistory"
					:check="check"
					@toggleCheck="handleToggleCheck"
					></FoodInfo>
				</template>
			</Card>
			<Card
			:info="stoneStatus"
			:hasDot="true"
			:dotClass="stoneStatusDotClass"
			:clickImageFunction="false"
			:clickCardFunction="true"
			@clickCard="goStoneStatusPage"
			></Card>
			<Card
			:info="todayOrderManage"
			:clickImageFunction="false"
			:clickCardFunction="true"
			@clickCard="goTodayOrderManagePage"
			></Card>
			<Card
			:info="FoodSatusManage"
			:clickImageFunction="false"
			:clickCardFunction="true"
			@clickCard="goFoodStatusPage"
			></Card>
		</view>
		
		<!-- 用户 -->
		<view v-else>
			<Card 
			:info="info"
			:clickImageFunction="false"
			:clickCardFunction="true"
			@clickCard="handleclickCard"
			name="foodInfo"
			>
				<template #foodInfo>
					<FoodInfo
					:isPull="isPull"
					:foodHistory="foodHistory"
					:check="check"
					@toggleCheck="handleToggleCheck"
					></FoodInfo>
				</template>
			</Card>
		</view>
	</view>
</template>

<script>
	/**
	 * @注册组件
	 */
	import LoginSlot from 'components/3/LoginSlot.vue'
	import Server from 'components/3/Server.vue'
	import Private from 'components/3/Private.vue'
	import TopNav from 'components/common/TopNav.vue'
	import Card from 'components/common/Card.vue'
	import FoodInfo from 'components/common/FoodInfo.vue'
	import ManagerCard from '@/components/common/ManagerCard.vue'
	
	export default {
		/**
		 * @声明组件
		 */
		components: {
			LoginSlot,
			Server,
			Private,
			TopNav,
			Card,
			FoodInfo,
			ManagerCard
		},
		data() {
			return {
				userInfo: [									// 用户信息
					{	
						pic: '/static/3/Ellipse 196.png',
						top: '林鸿飞',
						bottom: '技术部',
						phone: '15359548667',
						auth: '管理员' 
					}
				],
				clickLogin: false,							// 登录弹窗显示状态
			
				isShowServer: false,						// 服务协议显示状态
				isShowPrivate: false,						// 隐私政策显示状态
			
				serverContent: 'ServerContent',							// 服务协议内容
				privateContent: 'PrivateContent',						// 隐私政策内容
			
				info: [									
					{
						pic: '/static/3/Group 326.png',
						top: '个人订单记录',
						bottom: '查看个人历史订单数据',
						auth: null
					}
				],
				
				isPull: false, 										// 历史下拉
				foodHistory: [										// 历史订单
					{
						url_image: '/static/logo.png',
						list: ['啤酒鸭', '土豆烧茄子', '红烧狮子头', '千叶豆腐炒肉'],
						time: '2026-5-15 16:21:20',
						price: '16.00'
					}
				],
				check: '查看更多订单',
				
				
				
				/**
				 * @管理员
				 */
				leftinfo: [
					{
						title: '今日订餐人数',
						subtitle: '点击查看详情',
						data: 3,
						unit: '个',
						pic: '/static/manager/Frameleft.png'
					}
				],
				rightinfo: [
					{
						title: '今日订餐总额',
						subtitle: '点击查看详情',
						data: 321,
						unit: '元',
						pic: '/static/manager/Frameright.png'
					}
				],
				personOrderHistory: [
					{
						pic: '/static/manager/Group 326.png',
						top: '个人订单记录',
						bottom: '查看个人历史订单数据',
						auth: ''
					}
				],
				stoneStatus: [
					{
						pic: '/static/manager/Group 326 (1).png',
						top: '店铺状态管理',
						bottom: '未营业',
						auth: ''
					}
				],
				stoneStatusDotClass: 'bg-red',
				todayOrderManage: [
					{
						pic: '/static/manager/Group 326 (2).png',
						top: '今日订单管理',
						bottom: '管理当日订单状态',
						auth: ''
					}
				],
				FoodSatusManage: [
					{
						pic: '/static/manager/Group 326 (3).png',
						top: '菜品状态管理',
						bottom: '管理商品上下架状态',
						auth: ''
					}
				]
			}
		},
		computed: {
			isManager() {
				return this.userInfo[0].auth === '管理员'
			}
		},
		onShow() {
			this.refreshStoneStatusCard()
		},
		methods: {
			/**
			 * @组件通信
			 */
			saveUserInfo(userInfo) {
				this.userInfo[0].top = userInfo.top
				this.userInfo[0].bottom = userInfo.bottom
				this.userInfo[0].phone = userInfo.phone
				this.userInfo[0].pic = userInfo.pic || '/static/3/Ellipse 196.png'
			},
		  
			handleopenProtocol(value) {
				console.log(value);
				if (value === 'server') {
					this.isShowServer = true;
				} else if (value === 'private') {
					this.isShowPrivate = true;
				}
			},
			
			goBack(value) {
				console.log(value);
				if(value === 'server') {
					this.isShowServer = false;
				}
				
				if(value === 'private') {
					this.isShowPrivate = false;
				}
			},
			
			// 卡片
			handleclickCard() {
				this.isPull = !this.isPull
				
				if (this.isPull) {
						this.check = '查看更多订单'
						// 后端请求逻辑（请求三条）
						
						
						return
				}
		
				this.check = '查看更多订单'
				this.foodHistory = []
			},
			handleToggleCheck(value) {
				if (value === '查看更多订单') {
					this.check = '点击收起'
					// 后端请求逻辑（请求全部）
					
					
					return
				}
		
				if (value === '点击收起') {
					this.check = '查看更多订单'
					this.isPull = false
					this.foodHistory = []
				}
			},
			goUnorderPage() {
				uni.navigateTo({
					url: '/pages/index/manager/unorder'
				})
			},
			goOrderPricePage() {
				uni.navigateTo({
					url: '/pages/index/manager/orderprice'
				})
			},
			goTodayOrderManagePage() {
				uni.navigateTo({
					url: '/pages/index/manager/unorder'
				})
			},
			goStoneStatusPage() {
				uni.navigateTo({
					url: '/pages/index/manager/stonestatus'
				})
			},
			goFoodStatusPage() {
				uni.navigateTo({
					url: '/pages/index/manager/foodstatus'
				})
			},
			refreshStoneStatusCard() {
				// TODO: 后续替换为 GET /shops，根据后端返回的店铺营业状态刷新卡片
				const localShopList = uni.getStorageSync('shopList') || []
				let hasOpenShop = false
				if (Array.isArray(localShopList)) {
					for (let i = 0; i < localShopList.length; i++) {
						if (localShopList[i].status) {
							hasOpenShop = true
							break
						}
					}
				}
				if (hasOpenShop) {
					this.stoneStatus[0].bottom = '营业中'
					this.stoneStatusDotClass = 'bg-green'
					return
				}
				this.stoneStatus[0].bottom = '未营业'
				this.stoneStatusDotClass = 'bg-red'
			}
		}
	}
</script>

<style>
	.manager-row {
	    display: flex;
	    margin: 20rpx;
	  }
	
	  .manager-cell {
	    flex: 1;
	    min-width: 0;
	  }
	
	  .manager-cell-left {
	    margin-right: 20rpx;
	  }
</style>
