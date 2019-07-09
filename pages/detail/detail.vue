<template>
	<view class="page">
		<view class="video"><video :src="detailObj.trailer" id="movue" controls :poster="detailObj.poster" style="width: 100%;"></video></view>
		<view class="desc">
			<view class="img"><image :src="detailObj.poster" class="image" :lazy-load="true" @click="goCover"></image></view>
			<view class="content">
				<view class="c-item">
					<view class="name">{{ detailObj.name }}</view>
				</view>
				<view class="c-item">
					<view class="">{{ detailObj.basicInfo }}</view>
				</view>
				<view class="c-item">{{ detailObj.originalName }}</view>
				<view class="c-item">{{ detailObj.releaseDate }}</view>
				<view class="card">
					<view class="rate">
						<view class="r-name">综合评分:</view>
						<view class="score">{{ detailObj.score }}</view>
					</view>
					<view class="c-con">
						<view class="r-desc"><uni-rate :value="detailObj.score / 2" :max="5" size="12"></uni-rate></view>
						<view class="zan">{{ detailObj.prisedCounts }}人点赞</view>
					</view>
				</view>
			</view>
		</view>
		<view class="line"></view>
		<view class="de">
			<view class="d-name">剧情介绍:</view>
			<view class="d-content">{{ detailObj.plotDesc }}</view>
		</view>

		<view class="line"></view>
		<view class="de">
			<view class="d-name">演职人员:</view>
			<view class="d-content">
				<scroll-view scroll-x class="d-scroll">
					<view v-if="directors.length > 0" v-for="(item1, index1) in directors" :key="item1.id" class="s-item">
						<view class="img"><image :src="item1.photo" :lazy-load="true"></image></view>
						<view class="name">{{ item1.name }}</view>
						<view class="actName">{{ item1.actName }}</view>
					</view>
					<view v-if="actors.length > 0" v-for="(item2, index2) in actors" :key="item2.id" class="s-item">
						<view class="img"><image :src="item2.photo" :lazy-load="true"></image></view>
						<view class="name">{{ item2.name }}</view>
						<view class="actName">饰演:{{ item2.actName }}</view>
					</view>
				</scroll-view>
			</view>
		</view>

		<view class="line"></view>
		<view class="de" >
			<view class="d-name">剧照:</view>
			<view class="d-pic">
				<view class="plot-pics"  v-for="(item, index) in detailObj.plotPics" :key="index"><image :src="item" mode=""></image></view>
			</view>
		</view>
	</view>
</template>

<script>
import uniRate from '../../components/uni-rate/uni-rate.vue';
export default {
	components: {
		uniRate
	},
	data() {
		return {
			detailObj: {},
			directors: [],
			actors: []
		};
	},
	onLoad(options) {
		this.detailObj = JSON.parse(options.item);
		uni.setNavigationBarTitle({
			title: '电影详情'
		});
		this.getDirectors();
		this.getActors();
		this.detailObj.plotPics = JSON.parse(this.detailObj.plotPics)
	},
	// 仅仅只支持小程序分享 分享到微信群或者好友
	onShareAppMessage(res) {
		return {
			title: this.detailObj.name,
			path: '/pages/detail/detail?trailerId=' + this.detailObj.id
		};
	},
	onNavigationBarButtonTap(e) {
		let index = e.index;
		if (index === 0) {
		}
	},
	// 页面初次渲染完成 获得视频对象
	onReady() {
		let video = uni.createVideoContext('movie');
	},
	// 页面隐藏时 不播放
	onHide() {
		if (this.video) {
			this.video.pause();
		}
	},
	// 页面再次显示时 播放视频
	onShow() {
		if (this.video) {
			this.video.play();
		}
	},
	methods: {
		back() {
			let pages = getCurrentPages(),
				prevPage = null;
			if (pages.length > 1) {
				prevPage = pages[pages.length - 2];
			}
			uni.navigateBack();
		},
		// 封面预览
		goCover() {
			uni.navigateTo({
				url: `/pages/cover/cover?img=` + this.detailObj.poster
			});
		},
		// 获取导演
		getDirectors() {
			uni.request({
				url: `${this.$api}/search/staff/${this.detailObj.id}/1`,
				method: 'POST',
				data: {
					qq: '7929290'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res) {
						this.directors = res.data.data;
						// this.movieList = res.data.data.rows;
					}
				},
				fail: err => {
					console.log(err);
				}
			});
		},
		// 获取演员
		getActors() {
			uni.request({
				url: `${this.$api}/search/staff/${this.detailObj.id}/2`,
				method: 'POST',
				data: {
					qq: '7929290'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res) {
						this.actors = res.data.data;
						// this.movieList = res.data.data.rows;
					}
				},
				fail: err => {
					console.log(err);
				}
			});
		}
	}
};
</script>

<style scoped lang="scss">
.icon {
	width: 60upx;
	height: 60upx;
	position: absolute;
	left: 20upx;
	top: 20upx;
	z-index: 999;
	image {
		width: 60upx;
		height: 60upx;
	}
}
.desc {
	width: 100%;
	display: flex;
	align-items: center;
	justify-content: space-around;
	margin-top: 20upx;
	height: 460upx;
	.img {
		flex: 1;
		.image {
			width: 260upx;
			height: 300upx;
			margin: 0 30upx;
		}
	}
	.content {
		height: 300upx;
		flex: 1;
		display: flex;
		flex-direction: column;
		margin-right: 40upx;
		.c-item {
			color: #ccc;
			font-size: 24upx;
			margin: 5upx 0;
			.name {
				font-size: 36upx;
				color: #000;
				font-weight: 700;
			}
		}
	}
	.card {
		display: flex;
		align-items: center;
		background: #fff;
		margin-top: 20upx;
		box-shadow: 2upx 2upx 2upx #fff;
		height: 200upx;
		width: 96%;
		.rate {
			margin-left: 20upx;
			display: flex;
			flex-direction: column;
			justify-items: center;
			align-items: center;
			.r-name {
				font-size: 32upx;
			}
			.score {
				color: #feab2a;
				font-size: 32upx;
			}
		}
		.c-con {
			padding-top: 20upx;
			display: flex;
			flex-direction: column;
			align-items: center;
			margin-left: 50upx;
			.zan {
				font-size: 24upx;
				color: #ccc;
			}
		}
	}
}
.line {
	width: 90%;
	margin-left: 5%;
	background: #ccc;
	margin-top: 20upx;
	height: 2upx;
}
.de {
	padding: 0 20px;
	margin-top: 20upx;
	.d-name {
		font-size: 24upx;
		color: #ccc;
	}
	.d-content {
		font-size: 28upx;
		margin-top: 20upx;
	}
}
.d-scroll {
	width: 100%;
	white-space: nowrap;
	.s-item {
		display: inline-block;
		margin: 0 20upx;
		width: 120upx;
		.img {
			width: 120upx;
			height: 160upx;
			image {
				width: 120upx;
				height: 160upx;
			}
		}
		.name {
			font-size: 28upx;
			white-space: nowrap;
			text-overflow: ellipsis;
			overflow: hidden;
		}
		.actName {
			font-size: 24upx;
			color: #ccc;
			white-space: nowrap;
			text-overflow: ellipsis;
			overflow: hidden;
		}
	}
}
.d-pic {
	display: flex;
	align-items: center;
	flex-wrap: wrap;
	width: 100%;
	padding-bottom: 50upx;
	.plot-pics {
		width: 30%;
		height: 220upx;
		margin: 10upx 10upx;
		image {
			width: 180upx;
			height: 220upx;
		}
	}
}
</style>
