<template>
	<view
		v-if="isPull"
		:class="[mode === 'currentOrder' ? '' : 'padding-top-sm padding-bottom-sm', modeClass]"
		:style="rootStyle"
	>
		<view
			v-for="(item, index) in processedFoodHistory"
			:key="index"
			:class="['flex', itemClass]"
			:style="itemStyle"
		>
			<view class="flex align-center justify-center">
				<image
					v-if="item.imageSrc"
					:src="item.imageSrc"
					mode="aspectFill"
					:class="{ radius: mode === 'currentOrder' }"
					:style="imageStyle"
				></image>
				<view
					v-else
					:class="['bg-grey', 'light', { radius: mode === 'currentOrder' }]"
					:style="imageStyle"
				></view>
			</view>

			<view class="margin-left-sm justify-between flex flex-direction" :style="contentStyle">
				<view class="text-black text-lg text-cut" :class="{ 'text-bold': mode === 'currentOrder' }">
					{{ item.listText }}
				</view>

				<view v-if="mode === 'currentOrder'" class="flex align-center text-sm">
					<view class="text-gray">
						备注：{{ item.remark || '无' }}
					</view>
				</view>
				<view v-else class="flex align-center text-sm">
					<view class="text-gray">
						下单：{{ item.time }}
					</view>
				</view>
			</view>

			<view :class="[
				'text-bold',
				'text-lg',
				'text-price',
				'flex',
				'align-center',
				mode === 'currentOrder' ? 'justify-end text-black' : 'text-orange'
			]" style="flex: 1;">
				{{ item.price }}
			</view>
		</view>

		<view
			v-if="mode === 'currentOrder' && processedFoodHistory.length"
			class="flex align-center justify-between margin-top-lg text-gray text-sm"
			style="margin-top: 42rpx;"
		>
			<text>下单时间</text>
			<text>{{ processedFoodHistory[0].time }}</text>
		</view>
		
		<!-- 底部查看更多按钮 -->
		<view 
		v-if="showCheck"
		@click.stop="checkToggle"
		class="text-xl text-blue flex align-center justify-center"
		style="height: 80rpx;">
			<view>
				{{check}}
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'FoodInfo',
		props: {
			check: {
				type: String,
				default: '查看更多订单'
			},
			isPull: {
				type: Boolean,
				default: true
			},
			mode: {
				type: String,
				default: 'history'
			},
			showCheck: {
				type: Boolean,
				default: true
			},
			foodHistory: {
				type: Array,
				default: () => []
			}
		},
		computed: {
			modeClass() {
				return this.mode === 'currentOrder'
					? ''
					: 'solid radius margin-left-xl margin-right-xl'
			},
			rootStyle() {
				if (this.mode === 'currentOrder') {
					return 'flex: 1; min-height: 0; padding-top: 28rpx; padding-bottom: 0;'
				}

				return 'flex: 1; min-height: 0;'
			},
			itemClass() {
				return this.mode === 'currentOrder'
					? ''
					: 'margin-sm solid radius'
			},
			itemStyle() {
				return this.mode === 'currentOrder'
					? 'margin-top: 24rpx;'
					: ''
			},
			imageStyle() {
				const size = this.mode === 'currentOrder' ? '80rpx' : '150rpx'

				return `width: ${size}; height: ${size}; flex-shrink: 0;`
			},
			contentStyle() {
				const width = this.mode === 'currentOrder' ? '390rpx' : '55%'

				return `width: ${width}; flex-shrink: 0;`
			},
			processedFoodHistory() {
				return this.foodHistory.map(item => {
					return {
						...item,
						listText: this.formatFoodList(item.list),
						imageSrc: this.formatImage(item)
					}
				})
			}
		},
		methods: {
			formatFoodList(list) {
				if (!Array.isArray(list)) return ''
				if (!list.length) return ''

				if (typeof list[0] === 'string') {
					return list.join(' + ')
				}

				return list.map(item => {
					const count = Number(item.count || 0)
					return `${item.name}×${count}`
				}).join('+')
			},
			formatImage(item) {
				if (item.url_image) return item.url_image
				if (this.mode === 'currentOrder') return ''

				return '/static/logo.png'
			},
			checkToggle() {
				this.$emit('toggleCheck', this.check)
			}
		}
	}
</script>
