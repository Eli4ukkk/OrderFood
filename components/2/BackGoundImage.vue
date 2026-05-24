<template>
	<view class="store-bg-image">
		<image class="store-bg-image__img" :src="image" mode="aspectFill"></image>
	</view>
</template>

<script>
export default {
	name: 'BackGoundImage',
	props: {
		image: {
			type: String,
			default: '#'
		}
	},
}
</script>

<style>
	.store-bg-image {
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		overflow: hidden;
	}

	.store-bg-image__img {
		width: 100%;
		height: 100%;
		display: block;
	}
</style>
