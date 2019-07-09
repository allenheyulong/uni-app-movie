<template>
	<view class="page page-fill">
		<form @submit="formSubmitSex">
			<view class="page-block" style="margin-top: 20upx;">	
				<radio-group class="radio-sex" @change="sexChange">
					<label class="radio-single">
						<radio value="1" :checked="sex == 1"/>男
					</label>
					<label class="radio-single">
						<radio value="0" :checked="sex == 0"/>女
					</label>
				</radio-group>
			</view>
			<button type="primary" form-type="submit" class="submitBtn">提交</button>
			
		</form>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: {},
				sex: "-1"
			}
		},
		onLoad() {
			this.user = uni.getStorageSync('user')
			
		},
		methods: {
			sexChange(e) {
				this.sex = e.detail.value;
			},
			formSubmitSex () {
				uni.request({
					url: `${this.$api}/user/modifyUserinfo?qq=7929290`,
					method: 'POST',
					data: {
						userId: this.user.id,
						sex: this.sex
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

<style>

</style>
