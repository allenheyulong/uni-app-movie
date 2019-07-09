<template>
		<view class="page page-fill">
		<form @submit="formSubmitNickname">
			
			<view class="page-block" style="margin-top: 20upx;">
				<input 
					type="text"
					name="nickname"
					:value="user.nickname" 
					class="input"
					placeholder="请输入昵称"
					placeholder-class="graywords"
					maxlength="10"
					/>
			</view>
			<button type="primary" form-type="submit" class="submitBtn">提交</button>
		</form>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: {}
			}
		},
		onLoad() {
			this.user = uni.getStorageSync('user')
		},
		methods: {
			formSubmitNickname(e) {
				console.log(e)
				let nickname = e.detail.value.nickname
				uni.request({
					url: `${this.$api}/user/modifyUserinfo?qq=7929290`,
					method: 'POST',
					data: {
						userId: this.user.id,
						nickname: nickname
					},
					header: {
						"headerUserId": this.user.id,
						"headerUserToken": this.user.userUniqueToken
					},
					success: response => {
						let res = response.data
						if (res.status === 200) {
								let user = res.data
								uni.setStorageSync('user', user)
								uni.navigateBack({
									delta: 1
								})
								setTimeout(() => {
									uni.showToast({
										title: '修改成功',
										icon: 'success'
									})
								}, 100)
						} else if (res.status === 500 || res.status === 502 ) {
							uni.showToast({
								title: res.msg,
								image: '../../static/icos/error.png',
								duration: 2000
							})
						}
					},
					fail: () => {},
					complete: () => {}
				})
			}
		}
	}
</script>

<style scoped>
.page-fill {
	width:100%;
	height: 100%;
	position: absolute;
}

.graywords {
	color: #EAEAEA;
}

.input {
	height: 80upx;
	line-height: 80upx;
	width: 500upx;
	margin-left: 40upx;
}

.submitBtn {
	width: 95%;
	margin-top: 40upx;
}
</style>
