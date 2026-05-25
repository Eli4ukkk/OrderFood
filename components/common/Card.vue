<template>
	<view class="container" 
	>
		<view
		@click="handleClickCard" 
		class="user-info bg-white radius margin-top-sm margin-left-sm margin-right-sm  flex align-center"
		style="
		
		box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
		height: 200rpx;
		flex: 1;
		">	
			<!-- 图片 -->
			<view class="" @click="clickImage">
				<image 
				:src="info[0].pic" 
				mode="aspectFill"
				style="
				
				flex-shrink: 0;
				border-radius: 50%;
				width: 150rpx;
				height: 150rpx;
				left: 20rpx;
				"></image>
			</view>
			
			<!-- 上 + 下 -->
			<view class="flex flex-direction padding-xl justify-between"
			style="flex: 1;
			
			
			height: 100%;
			
			">
				<!-- 上 -->
				<view class="flex"
				style="
				height: 50rpx;
				">
					<view class="text-black text-bold text-xl"
					style="letter-spacing: 5rpx;
					line-height: 50rpx;
					">
						{{ info[0].top }}
					</view>
					<view 
						v-if="info[0].auth"
						class="margin-left-sm padding-left-sm padding-right-sm"
						style="line-height: 50rpx;
						border-radius: 50rpx;
						color: white;
						background-color: #FE8F29;
						
						
						">
						{{ info[0].auth}}
					</view>
				</view>
				
				<!-- 下 -->
				<view class="align-center flex text-black">
					<view 
					v-if="hasDot"
					style="
					width: 10rpx;
					height: 10rpx;
					border-radius: 50%;"
					:class="dotClass + ' margin-right-sm'"></view>
					{{ info[0].bottom }}
				</view>
			</view>
		</view>
		
		<!-- 槽 -->
		<slot :name="name"></slot>
		
	</view>
</template>

<script>
export default {
	name: 'Card',
	props: {
		info: {
			type: Array,
			default: () => [
				{
					pic: '/static/3/Group 326.png',
					top: 'top',
					bottom: 'bottom',
					auth: ''  
				}
			],
		},
		clickImageFunction: {
			type: Boolean,
			default: false
		},
		clickCardFunction: {
			type: Boolean,
			default: false
		},
		name: {
			type: String,
			default: 'login'
		},
		hasDot: {
			type: Boolean,
			default: false
		},
		dotClass: {
			type: String,
			default: 'bg-green'
		}
	},
	data() {
		return {
			
		}
	},
	methods: {
		clickImage() {
			if(!this.clickImageFunction) return;
			
			this.$emit('clickImage', true);
		},
		
		// 点击整个卡片
		handleClickCard() {
			if(!this.clickCardFunction) return;
			
			// console.log('点击了卡片');
			this.$emit('clickCard');
		}
	}
}
</script>

<style>
</style>
