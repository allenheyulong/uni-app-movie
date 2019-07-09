<template>
	<view class="page page-fill">
		<view class="pending-wapper"><image id="face" :src="tempFace" class="pending-face" mode="scaleToFill"></image></view>
		<view class="notice"><view class="notice-words">* 请从相册中选择等比宽高的图片噢~</view></view>
		<view class="footer-opertor">
			<view class="opertor-words" @click="changePendingFace">重新选择</view>
			<view class="opertor-words" @click="upload">确认上传</view>
		</view>
	</view>
</template>

<script>
export default {
	onLoad(e) {
		this.tempFace = e.img;
	},
	data() {
		return {
			tempFace: ''
		};
	},
	methods: {
		// 重新选择头像
		changePendingFace() {
			uni.chooseImage({
				count: 1,
				sizeType: ['compressed'],
				sourceType: ['album'],
				success: res => {
					let img = res.tempFilePaths[0];
					this.tempFace = img;
				}
			});
		},
		upload() {
			let user = uni.getStorageSync('user')
			uni.showLoading({
				mask: true,
				title: '上传中...'
			})
			uni.uploadFile({
				url: `${this.$api}/user/uploadFace?userId=${user.id}&qq=7929290`,
				filePath: this.tempFace,
				name: 'file',
				header: {
					"headerUserId": user.id,
					"headerUserToken": user.userUniqueToken
				},
				success: (response) => {
					// 	console.log(res.data)
					let res = JSON.parse(response.data)
					if (res.status === 200) {
						uni.setStorageSync('user', res.data)
						uni.showToast({
							title: "上传成功",
							icon:"success",
							duration: 2000
						})
						uni.navigateBack({
							delta:2
						})
					} else if (res.status === 502 || res.status === 500) {
						uni.showToast({
							title: res.data.msg,
							image: "../../static/icos/error.png"
						})
					}
				},
				complete: () => {
					uni.hideLoading()
				}
			})
		}
	}
};
</script>

<style scoped>
.page-fill {
	width: 100%;
	height: 100%;
	position: absolute;
	background: black;
}

.pending-wapper {
	display: flex;
	flex-direction: row;
	justify-content: center;

	margin-top: 40upx;
}
.pending-face {
	width: 600upx;
	height: 600upx;
}

.notice {
	display: flex;
	flex-direction: row;
	justify-content: flex-end;
}

.notice-words {
	color: gray;
	font-size: 13px;
	margin-top: 30upx;
	width: 600upx;
}

.footer-opertor {
	position: fixed;
	bottom: 0;

	border-top: #515050 solid 1px;
	width: 100%;

	display: flex;
	justify-content: space-around;
}
.opertor-words {
	color: #e8e5e5;
	font-size: 16px;
}
</style>
