<template>
	<view class="container">
		
		<TopNav
		:hasBack="true"
		title="美尔途订餐"
		@back="goBack"/>
		
		<!-- 导出年月 -->
		<view class="bg-white solid margin-sm padding-sm radius">
			<view
			style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
			class="flex text-black align-center padding-sm justify-center radius">
				导出年月
			</view>
			
			<!-- 选择日期 -->
			<view class="">
				<view 
				style="height: 90rpx; border: 1px solid #F99A66;"
				class="padding justify-between align-center flex solid margin-top-sm radius"
				@tap="openDateSelect">
					<view class="text-black text-lg">
						{{ selectedDate.year }}年
					</view>
				</view>
				<view 
				style="height: 90rpx; border: 1px solid #F99A66;"
				class="padding justify-between align-center flex solid margin-top-sm radius"
				@tap="openDateSelect">
					<view class="text-black text-lg">
						{{ selectedDate.month }}月
					</view>
				</view>
				<view 
				style="height: 90rpx; border: 1px solid #F99A66;"
				class="padding justify-between align-center flex solid margin-top-sm radius"
				@tap="openDateSelect">
					<view class="text-black text-lg">
						{{ selectedDate.day }}日
					</view>
				</view>
			</view>
		</view>
		
		<!-- 导出范围 -->
		<view class="bg-white solid margin-sm padding-sm radius">
			<view
			style=" background-color: #C9F6C9; height: 50rpx; width: 160rpx;"
			class="margin-bottom flex text-black align-center padding-sm justify-center radius">
				导出范围
			</view>
			<view>
				<view
				v-for="(item, index) in checkboxList1"
				:key="index"
				@tap="handleToggleDownloadScope(item.name)"
				class="flex align-center margin-bottom-sm">
					<view
					class="flex align-center justify-center"
					:style="isDownloadScopeChecked(item.name) ? activeCheckStyle : inactiveCheckStyle">
						<text
						v-if="isDownloadScopeChecked(item.name)"
						style="font-size: 26rpx;"
						class="cuIcon-check text-white"></text>
					</view>
					<view class="text-black margin-left-sm">
						{{ item.name }}
					</view>
				</view>
			</view>
			
			<view class="flex justify-center">
				<view
				@tap="handleExportReport"
				style="
				bottom: 50rpx;
				width: 200rpx;
				height: 70rpx;
				background-color: #C9F6C9;
				color: #007E39;
				position: fixed;
				box-shadow: 0 0 5px 5px rgba(0, 0, 0, 0.1);
				"
				class="solid flex align-center justify-center radius">
					导出报表
				</view>
			</view>
		</view>
		
		<!-- 日期弹窗 -->
		<view
			v-if="isDateSelect"
			@tap="handleCancel"
			style="position: fixed; left: 0; right: 0; top: 0; bottom: 0; z-index: 100200; background-color: rgba(0, 0, 0, 0.45); display: flex; align-items: flex-end;"
		>
			<view @tap.stop class="bg-white" style="width: 100%; border-radius: 24rpx 24rpx 0 0; overflow: hidden;">
				<view class="flex justify-between align-center solid-bottom padding-lr" style="height: 90rpx;">
					<view class="text-black" @tap="handleCancel">取消</view>
					<view class="text-bold text-black">请选择日期</view>
					<view style="color: #F99A66;" @tap="handleConfirm">确认</view>
				</view>
				<picker-view
					:value="datePickerValue"
					@change="handleDatePickerChange"
					style="width: 100%; height: 420rpx;"
				>
					<picker-view-column>
						<view
							v-for="(item, index) in yearList"
							:key="index"
							class="text-center"
							style="height: 70rpx; line-height: 70rpx;"
						>
							{{ item }}年
						</view>
					</picker-view-column>
					<picker-view-column>
						<view
							v-for="(item, index) in monthList"
							:key="index"
							class="text-center"
							style="height: 70rpx; line-height: 70rpx;"
						>
							{{ item }}月
						</view>
					</picker-view-column>
					<picker-view-column>
						<view
							v-for="(item, index) in dayList"
							:key="index"
							class="text-center"
							style="height: 70rpx; line-height: 70rpx;"
						>
							{{ item }}日
						</view>
					</picker-view-column>
				</picker-view>
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
			const currentTime = Date.now()
			const currentDate = new Date(currentTime)
			return {
				isDateSelect: false,
				dateInfo: currentTime,
				selectedDate: {
					year: currentDate.getFullYear(),
					month: currentDate.getMonth() + 1,
					day: currentDate.getDate(),
				},
				datePickerValue: [
					5,
					currentDate.getMonth(),
					currentDate.getDate() - 1,
				],
				yearList: [],
				monthList: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
				dayList: [],
				
				checkboxList1: [
					{
						name: '全部订单'
					},
					{
						name: '选中订单'
					}
				],
				downloadscope: [
					
				],
				activeCheckStyle: 'width: 40rpx; height: 40rpx; border-radius: 50%; border: 2rpx solid #F99A66; background-color: #F99A66;',
				inactiveCheckStyle: 'width: 40rpx; height: 40rpx; border-radius: 50%; border: 2rpx solid #999999; background-color: #FFFFFF;'
			}
		},
		created() {
			const currentYear = this.selectedDate.year
			this.yearList = []
			for (let year = currentYear - 5; year <= currentYear + 5; year += 1) {
				this.yearList.push(year)
			}
			this.refreshDayList()
		},
		methods: {
			openDateSelect() {
				const yearIndex = Math.max(0, this.yearList.indexOf(this.selectedDate.year))
				this.datePickerValue = [
					yearIndex,
					this.selectedDate.month - 1,
					this.selectedDate.day - 1,
				]
				this.isDateSelect = true
			},
			handleCancel() {
				this.isDateSelect = false
			},
			handleConfirm() {
				// 确认后的逻辑
				const year = this.yearList[this.datePickerValue[0]]
				const month = this.monthList[this.datePickerValue[1]]
				const day = this.dayList[this.datePickerValue[2]]
				const date = new Date(year, month - 1, day)
				this.dateInfo = date.getTime()
				this.setSelectedDate(this.dateInfo)
				this.isDateSelect = false
			},
			setSelectedDate(value) {
				const date = new Date(value)
				this.selectedDate = {
					year: date.getFullYear(),
					month: date.getMonth() + 1,
					day: date.getDate(),
				}
			},
			handleDatePickerChange(e) {
				const value = e.detail.value
				this.datePickerValue = value
				this.refreshDayList()
			},
			refreshDayList() {
				const year = this.yearList[this.datePickerValue[0]] || this.selectedDate.year
				const month = this.monthList[this.datePickerValue[1]] || this.selectedDate.month
				const dayCount = new Date(year, month, 0).getDate()
				this.dayList = Array.from({ length: dayCount }, (item, index) => index + 1)
				if (this.datePickerValue[2] >= dayCount) {
					this.datePickerValue = [
						this.datePickerValue[0],
						this.datePickerValue[1],
						dayCount - 1,
					]
				}
			},
			goBack() {
				uni.navigateBack()
			},
			handleToggleDownloadScope(scopeName) {
				const scopeIndex = this.downloadscope.indexOf(scopeName)
				if (scopeIndex === -1) {
					this.downloadscope.push(scopeName)
				} else {
					this.downloadscope.splice(scopeIndex, 1)
				}
				this.handleCheckBoxChange(this.downloadscope.slice())
			},
			isDownloadScopeChecked(scopeName) {
				return this.downloadscope.indexOf(scopeName) !== -1
			},
			handleCheckBoxChange(value) {
				this.downloadscope = value
				console.log(this.downloadscope)
			},
			handleExportReport() {
				if (!this.downloadscope.length) {
					uni.showToast({
						title: '请选择导出范围',
						icon: 'none'
					})
					return
				}

				const exportParams = {
					date: this.formatDate(this.dateInfo),
					scope: this.downloadscope,
				}

				console.log('导出报表参数', exportParams)
				this.requestExportReport(exportParams)
			},
			formatDate(value) {
				const date = new Date(value)
				const year = date.getFullYear()
				const month = `${date.getMonth() + 1}`.padStart(2, '0')
				const day = `${date.getDate()}`.padStart(2, '0')
				return `${year}-${month}-${day}`
			},
			requestExportReport(params) {
				// TODO: 后续在这里请求后端导出报表
				// return uni.request({
				// 	url: '',
				// 	method: 'POST',
				// 	data: params,
				// })
			}
		},
	}
</script>
