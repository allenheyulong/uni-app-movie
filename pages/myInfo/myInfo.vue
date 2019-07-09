<template>
	<view class="page page-fill">
		<view class="page-block info-list">
			<!-- 头像 -->
			<view class="item-wapper face-line-upbottom" @click="operator">
				<view class="info-words">头像</view>
				<view class="right-wapper">
					<image :src="user.faceImage" class="face"></image>
					<view class="arrow-block"><image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image></view>
				</view>
			</view>

			<view class="line-top"><view class="line"></view></view>

			<!-- 昵称 -->
			<view class="item-wapper" @click="modifyNickname">
				<view class="info-words">昵称</view>

				<view class="right-wapper">
					<view class="gray-fields">{{ user.nickname }}</view>
					<view class="arrow-block"><image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image></view>
				</view>
			</view>

			<view class="line-top"><view class="line"></view></view>

			<!-- 生日 -->
			<view class="item-wapper" @click="modifyBirthday">
				<view class="info-words">生日</view>

				<view class="right-wapper">
					<view class="gray-fields">{{ user.birthday }}</view>
					<view class="arrow-block"><image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image></view>
				</view>
			</view>

			<view class="line-top"><view class="line"></view></view>

			<!-- 性别 -->
			<view class="item-wapper" @click="modifySex">
				<view class="info-words">性别</view>

				<view class="right-wapper">
					<view class="gray-fields">
						<view v-if="user.sex == 1">男</view>
						<view v-else-if="user.sex == 0">女</view>
						<view v-else>未选择</view>
					</view>
					<view class="arrow-block"><image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image></view>
				</view>
			</view>
		</view>

		<view class="footer-wapper">
			<view class="footer-words" @click="cleanStorage">清理缓存</view>
			<view class="footer-words" style="margin-top: 10upx;" @click="logout">退出登录</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			user: {}
		};
	},
	onShow() {
		let user = uni.getStorageSync('user');
		this.user = user;
	},
	methods: {
		modifySex() {
			uni.navigateTo({
				url: '../gender/gender'
			});
		},
		modifyBirthday() {
			uni.navigateTo({
				url: '../birthday/birthday'
			});
		},
		modifyNickname() {
			uni.navigateTo({
				url: '../nickName/nickName'
			});
		},
		operator() {
			uni.showActionSheet({
				itemList: ['查看我的头像', '从手机相册中选择'],
				success: (res) => {
					let index = res.tapIndex
					if (index === 0) {
						// 预览头像
						let faceArr = []
						faceArr.push(this.user.faceImage)
						uni.previewImage({
							urls: faceArr,
							current: faceArr[0]
						})
					}
					if (index === 1) {
						uni.navigateTo({
							url:'../myFace/myFace'
						})
						uni.chooseImage({
							count: 1,
							sizeType:['compressed'],
							sourceType: ['album'],
							success: res => {
								let img = res.tempFilePaths[0]
								uni.navigateTo({
									url: '/pages/myFace/myFace?img=' + img
								})
							}
						})
					}
				}
			})
		},
		cleanStorage() {
			uni.clearStorageSync();
			uni.showToast({
				title: '清理缓存成功',
				icon: 'success'
			});
			setTimeout(() => {
				uni.switchTab({
					url: '/pages/me/me'
				});
			}, 1000);
		},
		logout() {
			uni.request({
				url: `${this.$api}/user/logout`,
				method: 'POST',
				data: {
					qq: '7929290',
					userId: this.user.id
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res.data.status === 200) {
						uni.removeStorageSync('user');
						uni.showToast({
							title: '退出成功',
							icon: 'success'
						});
						setTimeout(() => {
							uni.switchTab({
								url: '/pages/me/me'
							});
						}, 1000);
					}
				}
			});
		}
	}
};
</script>

<style>
/* 页面铺满屏幕 */
.page-fill {
	width: 100%;
	height: 100%;
	position: absolute;
}

.info-list {
	/* margin-top: 20upx; */
	padding: 0upx 30upx;
}

.info-words {
	color: #333333;
	font-size: 16px;
	width: 25%;
	line-height: 80upx;
	/* font-weight: bold; */
}

.face {
	width: 80upx;
	height: 80upx;
	border-radius: 50%;
}

.arrow-block {
	margin-left: 10upx;
	line-height: 86upx;
}

.arrow-ico {
	width: 30upx;
	height: 30upx;
}

.item-wapper {
	display: flex;
	/* margin-top: 20upx; */
	flex-direction: row;
	justify-content: flex-start;
}

.face-line-upbottom {
	margin-top: 20upx;
	/* margin-bottom: 20upx; */
	padding-top: 20upx;
	padding-bottom: 20upx;
}

.line-top {
	/* margin-top: 20upx; */
}

.right-wapper {
	width: 80%;
	display: flex;
	flex-direction: row;
	justify-content: flex-end;
}

.gray-fields {
	font-size: 14px;
	color: darkgray;
	line-height: 80upx;
}

/* 底部操作 start */
.footer-wapper {
	position: fixed;
	bottom: 0;

	display: flex;
	flex-direction: column;
	width: 100%;
}
.footer-words {
	text-align: center;
	background-color: white;
	padding: 20upx;

	color: #333333;
	font-size: 16px;
}
/* 底部操作 end */
</style>
