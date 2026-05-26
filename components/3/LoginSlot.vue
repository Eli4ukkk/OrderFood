<template>
	<view class="">
		<view
			v-if="clickLogin"
			@tap="clickQuit"
			style="position: fixed; left: 0; right: 0; top: 0; bottom: 0; z-index: 100100; background-color: rgba(0, 0, 0, 0.45); display: flex; align-items: center; justify-content: center;"
		>
			<view 
			@tap.stop
			class="flex flex-direction"
			style="height: 90%; width: 600rpx; overflow: hidden; background-color: #ffffff; border-radius: 20rpx; position: relative;">
				<view @tap="clickQuit" class="cuIcon-close" style="position: absolute; right: 24rpx; top: 24rpx; z-index: 2; color: #909399;"></view>
				<!-- TopBar -->
				<view class="solid-bottom" style="width: 100%; height: 100rpx; flex-shrink: 0;">
					<view class="margin-left-sm" style="line-height: 100rpx;">
						登录
					</view>
				</view>
				
				<scroll-view 
				scroll-y
				class="margin-top-sm" style="flex: 1; min-height: 0; width: 100%;">
					
					<view class="flex flex-direction align-center">
						<!-- 提示 -->
						<view
							class="text-center padding-sm radius"
							style="
							width: 80%;
							box-shadow: 5rpx 5rpx 10rpx rgba(0,0,0,0.3);
							border-left: 4rpx solid #5C9A7E;
							background-color: #F5F9FF;
							">
							账号仅限公司内部订餐登录使用
						</view>
						
						<!-- 姓名 + 部门 + 电话 -->
						<view class="margin-top" style="width: 80%;">
							真实姓名
							<view class="solid margin-top-sm" style="width: 100%;">
								<input
									:value="usernameLast"
									placeholder="请填写真实姓名"
									@input="changeUserName"
									style="height: 75rpx; padding: 0 20rpx; box-sizing: border-box;"
								/>
							</view>
						</view>
						<view
							@tap="clickBelong = true"
							class="margin-top"
							style="width: 80%;">
							所在部门
							<view class="solid margin-top-sm" style="width: 100%;">
								<view
									class="margin-left-sm"
									style="height: 75rpx; line-height: 75rpx;">
									{{ belongLast }}
								</view>
							</view>
						</view>
						<view class="margin-top" style="width: 80%;">
							手机号
							<view class="solid margin-top-sm" style="width: 100%;">
								<input
									:value="userphoneLast"
									placeholder="请填写手机号"
									type="number"
									@input="changeUserPhone"
									style="height: 75rpx; padding: 0 20rpx; box-sizing: border-box;"
								/>
							</view>
						</view>
						
						<!-- 头像 -->
						<view class="margin-top" style="width: 80%;">
							头像
								<view class="avatar-box margin-top-sm" style="margin: 0 auto;">
								<image v-if="avatarLast" :src="avatarLast" mode="aspectFill" class="avatar-img solid"></image>
								<image v-else @tap="chooseImage" class="avatar-img solid" :src="userInfo[0].pic" mode="aspectFill"></image>
							</view>
							<view class="text-center text-sm margin-top-sm">
								支持JPG，PNG格式，文件大小不超过2MB
							</view>
						</view>
						
						<!-- 协议 -->
						<view class="margin-top margin flex align-center">
							<view @tap="protocalToggle" style="width: 48rpx; height: 48rpx; line-height: 48rpx; flex-shrink: 0;">
								<text
									:class="ableToSave ? 'cuIcon-roundcheckfill' : 'cuIcon-round'"
									:style="{ color: ableToSave ? '#5C9A7E' : '#999999', fontSize: '38rpx' }"
								></text>
							</view>
							<view class="flex" style="flex-wrap: wrap; ">
								<text style="white-space: nowrap;">我已阅读并同意</text>
								<text style="text-decoration: underline; color: #005500; white-space: nowrap;" @tap="openProtocol('server')">《用户服务协议》</text>
								<text style="white-space: nowrap;">和</text>
								<text style="text-decoration: underline;  color: #005500; white-space: nowrap;" @tap="openProtocol('private')">《隐私政策》</text>
							</view>
							
						</view>
					</view>
					
				</scroll-view>
				<!-- 保存修改 -->
				<view 
				style="flex-shrink: 0; width: 100%;"
				class="padding solid-top flex justify-center">
					<view
						@tap="saveChange"
						class="text-center radius"
						:style="{ width: '70%', height: '80rpx', lineHeight: '80rpx', backgroundColor: ableToSave ? '#4CD964' : '#dddddd', color: '#ffffff' }"
					>
						保存修改
					</view>
				</view>
			</view>
		</view>

		<!-- 部门选择弹窗 -->
		<view
			v-if="clickBelong"
			@tap="cancelChange"
			style="position: fixed; left: 0; right: 0; top: 0; bottom: 0; z-index: 100200; background-color: rgba(0, 0, 0, 0.45); display: flex; align-items: flex-end;"
		>
			<view @tap.stop class="bg-white" style="width: 100%; border-radius: 24rpx 24rpx 0 0; overflow: hidden;">
				<view class="flex justify-between align-center solid-bottom padding-lr" style="height: 90rpx;">
					<view class="text-grey" @tap="cancelChange">取消</view>
					<view class="text-bold text-black">部门</view>
					<view style="color: #5C9A7E;" @tap="confirmChange">确认</view>
				</view>
				<picker-view
					:value="[belongIndex]"
					@change="changeBelong"
					style="width: 100%; height: 360rpx;"
				>
					<picker-view-column>
						<view
							v-for="(item, index) in parts[0]"
							:key="index"
							class="text-center"
							style="height: 72rpx; line-height: 72rpx;"
						>
							{{ item }}
						</view>
					</picker-view-column>
				</picker-view>
			</view>
		</view>
		
		<slot name="server"></slot>
		<slot name="private"></slot>
		
	</view>
</template>

<script>
	export default {
		name: 'LoginSlot',
		props: {
			clickLogin: {
				type: Boolean,
				default: true // 默认true用于测试
			},
			userInfo: {
				type: Array, // [{}] -> arr.[0]
				default: () => [
					{
						top: '',
						bottom: '',
						phone: '',
						pic: '/static/3/Ellipse 196.png',
					}
				]
			}
		},
		data() {
			const currentUser = this.userInfo[0] || {}
			const departments = ['技术部', '总经办', '财务部', '运营部']
			const currentBelong = currentUser.bottom || '技术部'
			
			return {
				
				usernameLast: currentUser.top || '', // 名字
				userphoneLast: currentUser.phone || '', // 电话
				// usernameCurrent: '', // 不需要last current，输入框可以直接显示，到时候按确定再一起送出去
				// userphoneCurrent: '',
				
				clickBelong: false, // 该组件页面是否显示
				belongLast: currentBelong, // 部门
				belongIndex: Math.max(0, departments.indexOf(currentBelong)),
				// belongCurrent: '技术部',
				parts: [				// 部门数组
					departments
				],
				
				nameTimeID: null,  // 用户名输入防抖
				phoneTimeID: null,  // 用户电话输入防抖
				
				ableToSave: false,   // 单选框
				
				avatarLast: null,      // 头像路径
				
			};
		},
		watch: {
			belongLast(newValue, oldValue) {
				// console.log(newValue);
				this.$emit('newBelong', newValue);
			}
		},
		methods: {
			clickQuit() {
				this.$emit('clickQuit', false);
			},
			confirmChange() {
				// 逻辑是在登录页填写 姓名 + 部门 + 手机号确实可以看到变化
				// 但是要是没有点击最下方确认修改按钮就会复原
				
				this.belongLast = this.parts[0][this.belongIndex];
				// 这里不用emit，到点击确认按钮将info一起用$emit送出去
				this.clickBelong = false;
			},
			cancelChange() {
				this.clickBelong = false;
			},
			changeBelong(e) {
				this.belongIndex = e.detail.value[0];
			},
			changeUserName(value) {
				const inputValue = value && value.detail ? value.detail.value : value
				//防抖
				if(this.nameTimeID) {
					clearTimeout(this.nameTimeID)
				}
				this.nameTimeID = setTimeout(()=>{
					this.usernameLast = inputValue;
					// console.log(this.usernameLast);
					// 到时候一起送出去
					// this.$emit();
				}, 500);
			},
			changeUserPhone(value) {
				const inputValue = value && value.detail ? value.detail.value : value
				// 防抖
				if(this.phoneTimeID) {
					clearTimeout(this.phoneTimeID)
				}
				this.phoneTimeID = setTimeout(()=>{
					this.userphoneLast = inputValue;
					// console.log(this.userphoneLast);
					// 到时候一起送出去
					// this.$emit();
				}, 500);
			},
			
			// 图片上传
			chooseImage() {
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['album', 'camera'],
					crop: {
						quality: 100,
						width: 250, 
						height: 250,
					},
					success: (res) => {
						this.avatarLast = res.tempFilePaths[0];
					},
					fail: (err) => {
						// 失败逻辑
					}
				})
			},
			
			// 协议
			protocalToggle() {
				this.ableToSave = ! this.ableToSave;
				// console.log(this.ableToSave);
			},
		
			// 保存修改
			saveChange() {
				if (!this.ableToSave) {
					return
				}
				// console.log('保存修改');	
				// 将 姓名 + 部门 + 手机号 + 头像送出去
				// usernameLast userphoneLast userphoneLast avatarLast
				// this.$emit(),
				const currentUser = this.userInfo[0] || {}
				const userInfo = {
					top: this.usernameLast || currentUser.top,
					bottom: this.belongLast || currentUser.bottom,
					phone: this.userphoneLast || currentUser.phone,
					pic: this.avatarLast || currentUser.pic,
				}
		
				this.$emit('saveChange', userInfo)
				this.$emit('clickQuit', false)
			},
			
			// 
			openProtocol(value) {
				// 打开协议页协议逻辑 
				this.$emit('openProtocol', value);
			}
		}
	}
</script>

<style>
	.avatar-box {
		display: flex;
		width: 200rpx;
		height: 200rpx;       /* 宽度用百分比，高度用 vw 保持等比例正方形 */
		/* 或直接写固定值：width: 100rpx; height: 100rpx; */
		border-radius: 50%;
		overflow: hidden;
	}
	.avatar-img {
		width: 100%;
		height: 100%;
	}
</style>
