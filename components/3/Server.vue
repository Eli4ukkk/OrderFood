<template>
	<view v-if="isShow" style="position: fixed; left: 0; right: 0; top: 0; bottom: 0; z-index: 100100; background-color: #ffffff;">
			<TopNav title="用户服务协议" @back="goBack"></TopNav>
			<view class="flex justify-center align-center" style="height: 100%;">
				<view 
				style="width: 90%; height: 90%;
				box-shadow: 5px 5px 5px 5px rgba(0, 0, 0, 0.3);
				
				"
				class="radius padding-xl solid">
					<scroll-view
					style="height: 100%;"
					scroll-y
					>
						<rich-text :nodes="content"></rich-text>
						<!-- 👇 测试用，加个几十行 -->
						<!-- <view v-for="i in 30" :key="i" style="padding: 10px 0;">
						测试滚动行 {{ i }}
						</view> -->
						
						<!-- 垫块 -->
						<view 
						style="
						
						width: 100%; height: 150rpx;"
						class="">
							
						</view>
					</scroll-view>
				</view>
			</view>
	</view>
</template>

<script>
	import TopNav from 'components/common/TopNav.vue'
	
	export default {
		components: {
			TopNav,
		},
		name: 'Server',
		props: {
			isShow: {
				type: Boolean,
				default: true // 用于测试
			},
			content: {
				type: String,
				default: '富文本'
			}
		},
		data() {
			return {
				
			}
		},
		methods: {
			goBack() {
				this.$emit('back', 'server')
			}
		}
	}
</script>

<style>

</style>
