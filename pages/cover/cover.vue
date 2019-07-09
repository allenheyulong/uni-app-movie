<template>
	<view class="container">
		<image :src="imgUrl" mode="widthFix" class="img" @longpress="opertar"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imgUrl: ''
			};
		},
		onLoad (options) {
			this.imgUrl = options.img
			uni.setNavigationBarTitle({
				title: '封面预览'
			})
		},
		methods: {
			opertar () {
				uni.showActionSheet({
					itemList: ['保存图片到本地'],
					success: res => {
						if (res.tapIndex === 0) {
							uni.showLoading({
								title:' 保存中'
							})
							uni.downloadFile({
								url: this.imgUrl, 
								success: result => {
									let tempFilePath = result.tempFilePath
									uni.saveImageToPhotosAlbum({
										filePath:tempFilePath,
										success: res => {
											uni.showToast({
												icon: 'success',
												title: '保存成功',
												duration: 2000
											})
										}
									})
								},
								complete: res => {
									uni.hideLoading()
								}
							})
						}
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.container {
		background: #000;
		height: 100%;
		width: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		position: fixed;
	}
</style>
