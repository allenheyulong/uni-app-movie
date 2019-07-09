<template>
	<view class="body">
		<form @submit="formSubmit">
			<view class="face-wapper">
				<image src="../../static/icos/default-face.png" class="face"></image>
			</view>
			
			<view class="info-wapper">
				<label class="words-lbl">账号</label>
				<input name="username" type="text" value="" class="input" placeholder="请输入用户名" placeholder-class="graywords"/>
			</view>
			
			<view class="info-wapper" style="margin-top: 40upx;">
				<label class="words-lbl">密码</label>
				<input name="password" type="text" value="" password="true" class="input" placeholder="请输入密码" placeholder-class="graywords"/>
			</view>
			
			<button type="primary" form-type="submit" style="margin-top: 60upx;width: 90%;">注册/登录</button>
		</form>
		
		
		<!-- 第三方登录H5不支持，所以隐藏掉 -->
		<!-- #ifndef H5 -->
			<view class="third-wapper">
				
				<view class="third-line">
					<view class="single-line">
						<view class="line"></view>
					</view>
					
					<view class="third-words">第三方账号登录</view>
					
					<view class="single-line">
						<view class="line"></view>
					</view>
				</view>
				
				<view class="third-icos-wapper">
					<!-- 5+app 用qq/微信/微博 登录 小程序用微信小程序登录 h5不支持 -->
					<!-- #ifdef APP-PLUS -->
						<image src="../../static/icos/weixin.png" data-logintype="weixin" @click="appOAuthLogin" class="third-ico"></image>
						<image src="../../static/icos/QQ.png" data-logintype="qq" @click="appOAuthLogin" class="third-ico" style="margin-left: 80upx;"></image>
						<image src="../../static/icos/weibo.png" data-logintype="sinaweibo" @click="appOAuthLogin" class="third-ico" style="margin-left: 80upx;"></image>
					<!-- #endif -->
					<!-- #ifdef MP-WEIXIN -->
						<button open-type='getUserInfo' @getuserinfo="wxLogin" class="third-btn-ico">
						</button>
					<!-- #endif -->
				</view>
			</view>
		<!-- #endif -->
	</view>
</template>

<script>
export default {
	data() {
		return {
			logintype: ''
		};
	},
	methods: {
		appOAuthLogin(e) {
			// 获取用户登录类型
			this.logintype = e.currentTarget.dataset.logintype 
			console.log(e.currentTarget);
			console.log(this.logintype)
			// 授权登录
			uni.login({
				provider: this.logintype,
				success: res => {
					console.log(res)
					// 成功以后 获取用户的信息
					uni.getUserInfo({
						provider: this.logintype,
						success: response => {
							// console.log(JSON.stringify(response));
							let face = ''
							let nickname = ''
							let openIdOrUid = ''
							let userInfo = response.userInfo
							if (this.logintype === 'weixin') {
								face = userInfo.avatarUrl
								nickname = userInfo.nickName
								openIdOrUid = userInfo.openId
							} else if (this.logintype === 'qq') {
								face = userInfo.figureurl_qq_2
								nickname = userInfo.nickname
								openIdOrUid = userInfo.openId
							} else if (this.logintype === 'sinaweibo') {
								face = userInfo.avatar_large
								nickname = userInfo.nickname
								openIdOrUid = userInfo.id
							}  
							// 使用一键登录
							uni.request({
								url: `${this.$api}/appUnionLogin/${this.logintype}?qq=7929290`,
								method: 'POST',
								data: {
									face: face,
									openIdOrUid: openIdOrUid,
									nickname: nickname
								},
								success: res => {
									if (res.data.status === 200) {
										console.log(res.data.data);
										let userInfo = res.data.data
										uni.setStorageSync('user', userInfo)
										uni.switchTab({
											url: '/pages/me/me'
										})
									}
									
								},
								fail: () => {},
								complete: () => {}
							});
						}
					})
				},
				fail: () => {},
				complete: () => {}
			});
		},
		// 微信小程序端微信登录
		wxLogin(e) {
			let user = e.detail.userInfo
			console.log(e);
			// 微信登录
			uni.login({
				provider: 'weixin',
				success: res => {
					// 获得微信登录的授权码
					let code = res.code
					uni.request({
						url: `${this.$api}/stu/mpWXLogin/${code}`,
						method: 'POST',
						header: {
							'qq': '7929290'
						},
						data: {
							avatarUrl: user.avatarUrl,
							nickName: user.nickName,
							whichMP: 1
						},
						success: res => {
							console.log(res);
							if (res.data.status === 200) {
								
							}
						}
					})
				}
			})
		},
		formSubmit(e) {
			let username = e.detail.value.username
			let password = e.detail.value.password
			uni.request({
				url: `${this.$api}/user/registOrLogin?qq=7929290`,
				method: 'POST',
				data: {
					username: username,
					password: password
				},
				'contentType': 'application/json;charset=utf-8',
				success: res => {
					if (res.data.status === 200) {
						let user = res.data.data;
						console.log(res);
						uni.setStorageSync('user', user)
						uni.showToast({
							icon:'success',
							title:'登录成功'
						})
						setTimeout(() => {
							uni.switchTab({
								url: '/pages/me/me'
							});
						}, 1000)
					} else if (res.data.status === 500) {
						uni.showToast({
							title: res.data.msg,
							image: '../../static/icos/error.png'
						})
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
.body {
	border-top: solid 1px #dbdbda;
	padding: 0 40upx;
}

/* 头像 start */
.face-wapper {
	display: flex;
	flex-direction: row;
	justify-content: center;

	margin-top: 120upx;
	margin-bottom: 120upx;
}

.face {
	width: 160upx;
	height: 160upx;
}
/* 头像 end */

/* 注册登录 start */
.info-wapper {
	display: flex;
	flex-direction: row;
	justify-content: center;

	border-bottom: solid 1px #dbdbda;

	padding-bottom: 20upx;
}

.words-lbl {
	color: #808080;
}

.input {
	width: 500upx;
	margin-left: 40upx;
}

.graywords {
	color: #eaeaea;
}

/* 注册登录 end */

/* 第三方登录 start */
.third-wapper {
	width: 100%;
	/* 固定底部 */
	/* bottom: 0;
		position: fixed; */

	margin-top: 60upx;
}

.third-line {
	display: flex;
	flex-direction: row;
	justify-content: center;
}

.third-words {
	color: #a9a9a9;
	font-size: 13px;

	display: flex;
	flex-direction: column;
	justify-content: center;
}

.single-line {
	padding: 15upx 20upx;
	width: 25%;
	align-items: center;
}

.third-icos-wapper {
	margin-top: 30upx;
	width: 100%;
	display: flex;
	flex-direction: row;
	justify-content: center;
	.item {
		flex: 1;
		display: flex;
		justify-content: center;
	}
}

.third-ico {
	width: 60upx;
	height: 60upx;
}

.third-btn-ico {
	background-image: url(http://122.152.205.72:88/group1/M00/00/05/CpoxxFxFO-yAOjTaAAATCIZEzRU503.png);
	width: 60upx;
	height: 60upx;
	background-color: white;
	background-size: 60upx 60upx;
	background-repeat: no-repeat;
	border: none;
	padding: 0;
}
button::after {
	border: none;
}
/* 第三方登录 end */
</style>
