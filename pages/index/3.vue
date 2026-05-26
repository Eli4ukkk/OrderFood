<template>
	<view class="margin-bottom-xl">
		<TopNav :hasBack="false" title="美尔途订餐" />

		<Card :info="userInfo" :clickImageFunction="true" @clickImage="handleUserCardTap">
			<template #login>
				<LoginSlot :userInfo="userInfo" @saveChange="saveUserInfo" @clickQuit="clickLogin = false"
					@openProtocol="handleopenProtocol" :clickLogin="clickLogin">

					<template #server>
						<Server @back="goBack" :isShow="isShowServer" :content="serverContent"></Server>
					</template>
					<template #private>
						<Private @back="goBack" :isShow="isShowPrivate" :content="privateContent"></Private>
					</template>

				</LoginSlot>
			</template>
		</Card>

		<!-- 管理员 -->
		<view v-if="isManager">
			<view class="manager-row">
				<view @tap="goUnorderPage" class="manager-cell manager-cell-left">
					<ManagerCard :info="leftinfo"></ManagerCard>
				</view>

				<view @tap="goOrderPricePage" class="manager-cell">
					<ManagerCard :info="rightinfo"></ManagerCard>
				</view>
			</view>

			<Card :info="personOrderHistory" :clickImageFunction="false" :clickCardFunction="true"
				@clickCard="handleclickCard" name="managerFoodInfo">
				<template #managerFoodInfo>
					<FoodInfo :isPull="isPull" :foodHistory="foodHistory" :check="check"
						@toggleCheck="handleToggleCheck"></FoodInfo>
				</template>
			</Card>
			<Card :info="stoneStatus" :hasDot="true" :dotClass="stoneStatusDotClass" :clickImageFunction="false"
				:clickCardFunction="true" @clickCard="goStoneStatusPage"></Card>
			<Card :info="todayOrderManage" :clickImageFunction="false" :clickCardFunction="true"
				@clickCard="goTodayOrderManagePage"></Card>
			<Card :info="FoodSatusManage" :clickImageFunction="false" :clickCardFunction="true"
				@clickCard="goFoodStatusPage"></Card>
		</view>

		<!-- 用户 -->
		<view v-else>
			<Card :info="info" :clickImageFunction="false" :clickCardFunction="true" @clickCard="handleclickCard"
				name="foodInfo">
				<template #foodInfo>
					<FoodInfo :isPull="isPull" :foodHistory="foodHistory" :check="check"
						@toggleCheck="handleToggleCheck"></FoodInfo>
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
				/**
				 * @对接后端用到的
				 */
				token: '',
				loginLoading: false,
				baseUrl: 'http://orderfood.com',

				userInfo: [ // 用户信息
					{
						pic: '/static/3/Ellipse 196.png',
						top: '林鸿飞',
						bottom: '技术部',
						phone: '15359548667',
						auth: '管理员'
					}
				],
				clickLogin: false, // 登录弹窗显示状态

				isShowServer: false, // 服务协议显示状态
				isShowPrivate: false, // 隐私政策显示状态

				serverContent: 'ServerContent', // 服务协议内容
				privateContent: 'PrivateContent', // 隐私政策内容

				info: [{
					pic: '/static/3/Group 326.png',
					top: '个人订单记录',
					bottom: '查看个人历史订单数据',
					auth: null
				}],

				isPull: false, // 历史下拉
				foodHistory: [ // 历史订单
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
				leftinfo: [{
					title: '今日订餐人数',
					subtitle: '点击查看详情',
					data: 3,
					unit: '个',
					pic: '/static/manager/Frameleft.png'
				}],
				rightinfo: [{
					title: '今日订餐总额',
					subtitle: '点击查看详情',
					data: 321,
					unit: '元',
					pic: '/static/manager/Frameright.png'
				}],
				personOrderHistory: [{
					pic: '/static/manager/Group 326.png',
					top: '个人订单记录',
					bottom: '查看个人历史订单数据',
					auth: ''
				}],
				stoneStatus: [{
					pic: '/static/manager/Group 326 (1).png',
					top: '店铺状态管理',
					bottom: '未营业',
					auth: ''
				}],
				stoneStatusDotClass: 'bg-red',
				todayOrderManage: [{
					pic: '/static/manager/Group 326 (2).png',
					top: '今日订单管理',
					bottom: '管理当日订单状态',
					auth: ''
				}],
				FoodSatusManage: [{
					pic: '/static/manager/Group 326 (3).png',
					top: '菜品状态管理',
					bottom: '管理商品上下架状态',
					auth: ''
				}]
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
		onLoad() {
			this.initLoginStatus()
			// this.testWechatLogin()
		},
		methods: {
			/**
			 * @组件通信
			 */
			saveUserInfo(userInfo) {
			  if (this.isNeedUploadAvatar(userInfo.pic)) {
			    this.uploadAvatarAndSave(userInfo)
			    return
			  }
			
			  this.updateUserInfo(userInfo)
			},
			
			isNeedUploadAvatar(pic) {
			  if (!pic) {
			    return false
			  }
			
			  if (pic.indexOf('http://tmp/') === 0) {
			    return true
			  }
			
			  if (pic.indexOf('wxfile://') === 0) {
			    return true
			  }
			
			  if (pic.indexOf('/tmp/') === 0) {
			    return true
			  }
			
			  return false
			},
			/**
			 * @对接后端登录
			 */
			handleUserCardTap() {
				if (this.token) {
					this.clickLogin = true
					return
				}

				this.loginByWechatMini()
			},
			initLoginStatus() {
				const token = uni.getStorageSync('token')
				const userinfo = uni.getStorageSync('userinfo')

				if (!token) {
					return
				}

				this.token = token

				if (userinfo) {
					this.applyLoginUserInfo(userinfo)
				}
			},
			loginByWechatMini() {
				if (this.loginLoading) {
					return
				}

				this.loginLoading = true

				uni.login({
					provider: 'weixin',
					success: (loginRes) => {
						this.requestWechatLogin(loginRes.code)
					},
					fail: () => {
						this.loginLoading = false
						uni.showToast({
							title: '微信登录失败',
							icon: 'none'
						})
					}
				})
			},
			requestWechatLogin(code) {
				if (!code) {
					this.loginLoading = false
					uni.showToast({
						title: '微信未返回code',
						icon: 'none'
					})
					return
				}

				uni.request({
					url: this.baseUrl + '/api/user/wechatmini',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						code: code
					},
					success: (res) => {
						console.log('登录结果', res.data)

						const result = res.data || {}

						if (result.code !== 1) {
							uni.showToast({
								title: result.msg || '微信登录失败',
								icon: 'none'
							})
							return
						}

						const data = result.data || {}
						const userinfo = data.userinfo || {}
						const token = userinfo.token || ''

						if (!token) {
							uni.showToast({
								title: '后端未返回 token',
								icon: 'none'
							})
							return
						}

						this.token = token
						uni.setStorageSync('token', token)
						uni.setStorageSync('userinfo', userinfo)
						// 测试有没有token
						// this.testSaveProfile(token)
						
						this.applyLoginUserInfo(userinfo)
						this.clickLogin = true
					},
					fail: () => {
						uni.showToast({
							title: '登录接口请求失败',
							icon: 'none'
						})
					},
					complete: () => {
						this.loginLoading = false
					}
				})
			},
			applyLoginUserInfo(userinfo) {
			  this.userInfo[0].top = userinfo.nickname || this.userInfo[0].top
			  this.userInfo[0].phone = userinfo.mobile || userinfo.phone || this.userInfo[0].phone
			  this.userInfo[0].bottom = userinfo.department || this.userInfo[0].bottom
			  this.userInfo[0].pic = this.formatAvatarUrl(userinfo.avatar || userinfo.pic || this.userInfo[0].pic)
			  this.userInfo[0].auth = userinfo.role || userinfo.auth || this.userInfo[0].auth
			},
			formatAvatarUrl(avatar) {
			  if (!avatar) {
			    return this.userInfo[0].pic
			  }
			
			  if (
			    avatar.indexOf('http') === 0 ||
			    avatar.indexOf('/static/') === 0 ||
			    avatar.indexOf('data:image') === 0
			  ) {
			    return avatar
			  }
			
			  return this.baseUrl + avatar
			},
			// 提交用户信息
			updateUserInfo(userInfo) {
			  uni.request({
			    url: this.baseUrl + '/api/user/profile',
			    method: 'POST',
			    header: {
			      token: this.token,
			      'content-type': 'application/x-www-form-urlencoded'
			    },
			    data: {
			      username: userInfo.top,
			      department: userInfo.bottom,
			      phone: userInfo.phone,
			      avatar: userInfo.pic
			    },
			    success: (res) => {
			      console.log('保存用户资料结果', res.data)
			
			      const result = res.data || {}
			
			      if (result.code !== 1) {
			        uni.showToast({
			          title: result.msg || '保存失败',
			          icon: 'none'
			        })
			        return
			      }
			
			      const newUserInfo = result.data && result.data.userinfo
			
			      if (!newUserInfo) {
			        uni.showToast({
			          title: '后端未返回用户信息',
			          icon: 'none'
			        })
			        return
			      }
			
			      this.token = newUserInfo.token || this.token
			
			      uni.setStorageSync('token', this.token)
			      uni.setStorageSync('userinfo', newUserInfo)
			
			      this.applyLoginUserInfo(newUserInfo)
			
			      this.clickLogin = false
			
			      uni.showToast({
			        title: '保存成功',
			        icon: 'success'
			      })
			    },
			    fail: () => {
			      uni.showToast({
			        title: '保存资料请求失败',
			        icon: 'none'
			      })
			    }
			  })
			},
			uploadAvatarAndSave(userInfo) {
				// 处理头像

				uni.uploadFile({
					url: this.baseUrl + '/api/common/upload',
					filePath: userInfo.pic,
					name: 'file',
					header: {
						token: this.token
					},
					success: (res) => {
						const result = JSON.parse(res.data || '{}')
						const avatarUrl = result.data && result.data.url

						this.updateUserInfo({
							...userInfo,
							pic: avatarUrl
						})
					}
				})
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
				if (value === 'server') {
					this.isShowServer = false;
				}

				if (value === 'private') {
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
			},



			/**
			 * @测试接口
			 */
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