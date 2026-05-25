<template>
	<view class="container">
		
		<!-- 顶部 -->
		<TopNav  
		:hasBack="true" 
		title="美尔途订餐"
		@back="goBack">
		</TopNav>
		<!-- 个人信息 - 卡片 -->
		<Card 
		:info="userinfo"
		></Card>
		
		<!-- 订单信息 - 卡片 -->
		 <view class="manager-row">
		    <view class="manager-cell manager-cell-left">
		      <ManagerCard :info="leftinfo"></ManagerCard>
		    </view>
		
		    <view class="manager-cell">
		      <ManagerCard :info="rightinfo"></ManagerCard>
		    </view>
		  </view>
		
		<!-- 个人订单记录 -->
		<Card
		:info="personOrderHistory"
		></Card>
		
		<!-- 店铺状态管理 -->
		<Card
		:info="stoneStatus"
		:hasDot="true"
		:clickImageFunction="false"
		:clickCardFunction="true"
		@clickCard="goStoneStatusPage"
		></Card>
		
		<!-- 今日订单管理 -->
		<Card
		:info="todayOrderManage"
		:clickImageFunction="false"
		:clickCardFunction="true"
		@clickCard="goTodayOrderManagePage"
		></Card>
		
		<!-- 菜品状态管理 -->
		<Card
		class=""
		:info="FoodSatusManage"
		></Card>
		
	</view>
</template>

<script>
	/**
	 * @注册组件
	 */
	import TopNav from "@/components/common/TopNav.vue";
	import Card from '@/components/common/Card.vue'
	import ManagerCard from "@/components/common/ManagerCard.vue";
	
	export default {
		/**
		 * @声明组件
		 */
		components: {
			TopNav,
			Card,
			ManagerCard,
		},
		data() {
			return {
				userinfo: [
					{
						pic: '/static/3/Ellipse 196.png',
						top: '工鸟飞',
						bottom: '技术部',
						auth: '管理员'
					}
				],
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
						bottom: '营业中',
						auth: ''
					}
				],
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
		methods: {
			goBack() {
				uni.navigateBack()
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
