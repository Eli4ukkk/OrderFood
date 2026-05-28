<template>
	<!-- 店铺选择弹窗 -->
	<view v-if="visible" @tap="closeShopDialog" style="
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	z-index: 1002;
	background-color: rgba(0, 0, 0, 0.24);" class="flex align-center justify-center">
		<view @tap.stop style="
		width: 660rpx;
		background-color: #FFFFFF;
		border-radius: 18rpx;
		box-sizing: border-box;
		padding: 28rpx 30rpx 30rpx 30rpx;" class="flex flex-direction">
			<view style="
			height: 64rpx;
			font-size: 34rpx;
			line-height: 64rpx;
			color: #111111;" class="text-bold">
				选择店铺
			</view>

			<scroll-view scroll-y style="
			width: 100%;
			height: 500rpx;
			margin-top: 18rpx;">
				<view v-if="shopList.length === 0" style="
				height: 160rpx;
				font-size: 28rpx;
				color: #777777;" class="flex align-center justify-center">
					暂无店铺
				</view>
				<view v-for="item in shopList" :key="item.id" @tap="selectShop(item)" style="
				min-height: 86rpx;
				padding: 0 18rpx;
				border-bottom: 2rpx solid #EEEEEE;
				box-sizing: border-box;" class="flex align-center justify-between">
					<text style="font-size: 30rpx; color: #111111;" class="text-cut">
						{{item.name}}
					</text>
					<text v-if="selectedShopId === item.id" style="font-size: 30rpx; color: #F55B08;"
						class="cuIcon-check"></text>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'ChooseStoneModel',
		props: {
			visible: Boolean,
			baseUrl: String,
			token: String,
			selectedShopId: [String, Number]
		},
		data() {
			return {
				shopList: []
			}
		},
		watch: {
			visible(value) {
				if (value) {
					this.requestShopList();
				}
			}
		},
		methods: {
			closeShopDialog() {
				this.$emit('close');
			},
			selectShop(item) {
				this.$emit('select', item);
				this.closeShopDialog();
			},
			requestShopList() {
				// TODO: 后续在这里请求后端获取全部店铺
				// GET /shops
				// 返回数据建议映射为：[{ id: shopId, name: shopName }]
				uni.request({
					url: this.baseUrl + '/api/foodd.searchallstone/searchall',
					method: 'GET',
					header: {
						token: this.token
					},
					success: (res) => {
						if (Number(res.data.code) === 1) {
							const list = res.data.data.list || [];

							this.shopList = list.map(item => {
								return {
									id: item.id,
									name: item.name
								}
							});
						}
					}
				});
			}
		}
	}
</script>
