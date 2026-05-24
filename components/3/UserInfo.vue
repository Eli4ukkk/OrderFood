<template>
	<view class="container">
		<view class="user-info bg-white radius margin-top-sm margin-left-sm margin-right-sm flex align-center"
		style="
		
		box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
		height: 200rpx;
		flex: 1;
		">
			<view class="" @click="clickImage">
				<image 
				:src="userInfo[0].avatar" 
				mode="aspectFill"
				style="
				
				flex-shrink: 0;
				border-radius: 50%;
				width: 150rpx;
				height: 150rpx;
				left: 20rpx;
				"></image>
			</view>
			
			<!-- 名字 + 部门 -->
			<view class="flex flex-direction padding-xl justify-between"
			style="flex: 1;
			
			
			height: 100%;
			
			">
				<!-- 名字 -->
				<view class="flex"
				style="
				height: 50rpx;
				">
					<view class="text-black text-bold text-xl"
					style="letter-spacing: 5rpx;
					line-height: 50rpx;
					">
						{{ userInfo[0].name }}
					</view>
					<view 
						v-if="userInfo[0].auth"
						class="margin-left-sm padding-left-sm padding-right-sm"
						style="line-height: 50rpx;
						border-radius: 50rpx;
						color: white;
						background-color: #FE8F29;
						
						
						">
						{{ userInfo[0].auth}}
					</view>
				</view>
				
				<!-- 部门 -->
				<view class="text-black">
					{{ userInfo[0].belong }}
				</view>
			</view>
		</view>
		
		<!-- 登录槽 -->
		<slot name="login"></slot>
		
	</view>
</template>

<script>
export default {
	name: 'UserInfo',
	props: {
		userInfo: {
			type: Array,
			default: () => [
				{
					avatar: '/static/3/Ellipse 196.png',
					name: '用户名',
					belong: '部门',
					auth: '权限'  
				}
			]
		}
	},
	data() {
		return {
			
		}
	},
	methods: {
		clickImage() {
			
			this.$emit('clickImage', true);
		}
	}
}
</script>

<style>
</style>