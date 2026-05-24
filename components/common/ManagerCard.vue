<template>
	<view 
	style="
	flex: 1;
	min-width: 0rpx;
	height: 250rpx;
	box-shadow: 0 0 5rpx 5rpx rgba(0, 0, 0, 0.2);
	"
	class="bg-white solid flex container radius">
		
		<!-- 左边 title subtitle data -->
		<view 
		style="flex: 1;"
		class="margin-top margin-left margin-bottom">
			<view 
			style="
			
			"
			class="flex flex-direction">
				<view 
				style=""
				class="text-bold text-black">
					{{info[0].title}}
				</view>
				<view 
				style="
				
				"
				class="text-gray text-sm">
					{{info[0].subtitle}}
				</view>
				<view
				style="
				gap: 10rpx;
				
				"
				class="flex align-center">
					<!-- 数据 data -->
					<view 
					style="
					color: #FF992B;
					"
					class="text-sl text-black">
						{{info[0].data}}
					</view>
					<!-- 单位 unit -->
					<view class="text-black">
						{{info[0].unit}}
					</view>
				</view>
			</view>
		</view>
		
		<!-- 右边 图片 -->
		<view
			style="
			width: 120rpx;
			height: 120rpx;
			max-width: 40%;
			flex-shrink: 0;
			overflow: hidden;
			"
			class="flex self-end"
		  >
			<image
			  style="
			  width: 100%;
			  height: 100%;
			  display: block;
			  "
			  :src="info[0].pic"
			  mode="aspectFit"
			></image>
		  </view>
		
	</view>
</template>

<script>
	export default {
		name: "ManagerCard",
		props: {
			info: {
				type: Array,
				default: () => [
					{
						title: 'title',
						subtitle: 'subtitle',
						data: 'data',
						unit: 'unit', // 个 元
						pic: '/static/3/Ellipse 196.png',
					}
				]
			}
		},
		data() {
			return {
				
			};
		},
		methods: {
			
		}
	}
</script>

<style>
</style>
