<template>
	<view class="page">
		<view>
			<swiper class="carousel" :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" indicator-color="#000" indicator-active-color="#fff">
				<swiper-item v-for="(item, index) in banners" :key="item.id"><image :src="item.image" mode="" class="item"></image></swiper-item>
			</swiper>
		</view>

		<view>
			<view class="page-block hot">
				<view class="h-desc">
					<view class="h-img"><image src="/static/icos/hot.png" :lazy-load="true"></image></view>
					<view class="h-title"><div>热门超英</div></view>
				</view>
			</view>
		</view>

		<scroll-view scroll-x class="page-block scroll">
			<view class="s-desc" v-for="(item, index) in movieList" :key="item.id" @click="goDetail(item)">
				<view class="img"><image :src="item.cover" :lazy-load="true"></image></view>
				<view class="title">{{ item.name }}</view>
				<view class="rate">
					<view class="r-desc"><uni-rate :value="item.score / 2" :max="5" size="12"></uni-rate></view>
					<view class="score">{{ item.score }}</view>
				</view>
			</view>
		</scroll-view>

		<view>
			<view class="page-block hot">
				<view class="h-desc">
					<view class="h-img"><image src="/static/icos/interest.png" :lazy-load="true"></image></view>
					<view class="h-title"><div>热门预告</div></view>
				</view>
			</view>
		</view>

		<view class="v-con">
			<view class="v-desc" v-for="(item, index) in trailer" :key="item.id">
				<view class="v-item"><video :src="item.trailer" :id="item.id" :poster="item.cover" controls class="video"  @play="play(item.id)"></video></view>
			</view>
		</view>

		<view class="page-block guess-u-like">
			<view  class="single-like-movie" v-for="(item, index) in likes" :key="item.id">
				<image :src="item.cover" class="like-movie" :lazy-load="true" @click="goDetail(item)"></image>
				<view class="movie-desc">
					<view class="movie-title">{{ item.name }}</view>
					<view class="score"><uni-rate size="12" :max="5" :value="item.score"></uni-rate></view>
					<view class="movie-info">{{ item.basicInfo }}</view>
					<view class="movie-info">上映时间: {{ item.createTime }}</view>
				</view>
				<view class="movie-oper"   @click="praiseMe">
					<image src="/static/icos/praise.png" class="praise-ico" :lazy-load="true"></image>
					<view class="praise-me">
						赞一下
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import uniRate from '../../components/uni-rate/uni-rate.vue'
 
export default {
	name: 'index',
	components: {
		uniRate
	},
	data() {
		return {
			banners: [],
			movieList: [], //电影
			trailer: [], // 预告
			likes: [], // 猜你喜欢
			isPull: false,
			
		};
	},
	onLoad() {
		this.getData();
		this.getMovieList();
		this.getTrailer();
		this.getLikes();
	},
	onPullDownRefresh() {
		this.isPull = true
		this.getLikes()
	},
	onHide () {
		if (this.video) {
			this.video.pause()
		}
	},
	methods: {
		// 视频播放
		play (id) {
			this.video = uni.createVideoContext(id)
			this.trailer.map(item => {
				if (item.id !== id) {
					uni.createVideoContext(item.id).pause()
				}
			})
		},
		// 去详情页
		goDetail(item) {
			uni.navigateTo({
				url: '/pages/detail/detail?item=' + JSON.stringify(item)
			});
		},
		// 获取数据
		getData() {
			uni.showLoading({
				title:"加载中..."
			})
			uni.request({
				url: `${this.$api}/index/carousel/list`,
				method: 'POST',
				data: {
					qq: '7929290'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res.data.status === 200) {
						uni.hideLoading()
						this.banners = res.data.data;
					}
				}
			});
		},
		// 获取电影列表
		getMovieList() {
			uni.showLoading({
				title:"加载中..."
			})
			uni.request({
				url: `${this.$api}/index/movie/hot`,
				method: 'POST',
				data: {
					qq: '7929290',
					type: 'superhero'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res) {
						uni.hideLoading()
						this.movieList = res.data.data
					}
					
				}
			});
		},
		 // 获取播放视频
		getTrailer() {
			uni.showLoading({
				title:"加载中..."
			})
			uni.request({
				url: `${this.$api}/index/movie/hot`,
				method: 'POST',
				data: {
					qq: '7929290',
					type: 'trailer'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res) {
						uni.hideLoading()
						this.trailer = res.data.data;
					}
					
				}
			});
		},
		// 获取猜你喜欢
		getLikes() {
			uni.request({
				url: `${this.$api}/index/guessULike`,
				method: 'POST',
				data: {
					qq: '7929290',
					type: 'trailer'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res) {
						uni.stopPullDownRefresh()
						this.likes = res.data.data;
						this.likes.map(item => {
							item.createTime = this.$moment(item.createTime).format('YYYY-MM-DD');
						})
						if (this.isPull) {
							setTimeout(() => {
								uni.showToast({
									icon: 'success',
									title: '刷新成功'
								})
							}, 500)
						}
					}
				}
			})
		},
		// 点赞
		praiseMe () {
			uni.showToast({
				icon:'success',
				title: '点赞成功'
			})
		}
	}
};
</script>

<style lang="scss" scoped>
.carousel {
	width: 100%;
	height: 440upx;
	.item {
		width: 100%;
		image: {
			width: 100%;
		}
	}
}
.hot {
	margin-top: 12upx;
	padding: 20upx;
	.h-desc {
		display: flex;
		align-items: center;
		.h-img {
			width: 30upx;
			height: 30upx;
			display: flex;
			align-items: center;
			image {
				width: 100%;
				height: 100%;
			}
		}
		.h-title {
			direction: flex;
			align-items: center;
			margin-left: 20upx;
			font-size: 24upx;
			font-weight: bold;
		}
	}
}
.scroll {
	width: 100%;
	white-space: nowrap;
	.s-desc {
		display: inline-block;
		text-align: center;
		margin-right: 20upx;
		&:first-child {
			margin-left: 20upx;
		}
		.img {
			width: 150upx;
			height: 150upx;
			text-align: center;
			margin-left: 6upx;
			image {
				width: 100%;
				height: 100%;
			}
		}
		.title {
			width: 150upx;
			font-size: 24upx;
			white-space: nowrap;
			overflow: hidden;
			text-overflow: ellipsis;
		}
		.rate {
			width: 150upx;
			display: flex;
			align-items: center;
			font-size: 28upx;
			.r-desc {
			}
			.score {
				font-size: 24upx;
			}
		}
	}
}
.v-con {
	display: flex;
	align-items: center;
	flex-wrap: wrap;
	.v-desc {
		width: 45%;
		height: 150px;
		margin: 15upx 18upx;
		.v-item {
			width: 100%;
			height: 150px;
			.video {
				width: 100%;
				height: 150px;
			}
		}
	}
}
.guess-u-like {
	display: flex;
	flex-direction: column;
}
.single-like-movie {
	display: flex;
	flex-direction: row;
	justify-content: space-between;
	padding: 30upx 20upx;
}
.like-movie {
	width: 180upx;
	height: 240upx;
	border-radius: 3%;
}
.movie-desc {
	width: 340upx;
	display: flex;
	flex-direction: column;
}
.movie-title {
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
}
.movie-info {
	color: #808080;
	font-size: 14px;
}

/* 点赞css */
.movie-oper {
	width: 140upx;
	display: flex;
	flex-direction: column;
	justify-content: center;
	
	border-left: dashed 2px;
	border-left-color: #dbdbda;
}

.praise-ico {
	width: 40upx;
	height: 40upx;
	align-self: center;
}

.praise-me {
	font-size: 14px;
	color: #feab2a;
	align-self: center;
}

.animation-opacity {
	font-weight: bold;
	opacity: 0;
}
</style>
