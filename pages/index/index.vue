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
					<view class="h-img"><image src="/static/icos/hot.png" mode=""></image></view>
					<view class="h-title"><div>热门超英</div></view>
				</view>
			</view>
		</view>

		<scroll-view scroll-x class="page-block scroll">
			<view class="s-desc" v-for="(item, index) in movieList" :key="item.id">
				<view class="img"><image :src="item.cover" mode=""></image></view>
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
					<view class="h-img"><image src="/static/icos/interest.png" mode=""></image></view>
					<view class="h-title"><div>热门预告</div></view>
				</view>
			</view>
		</view>

		<view class="v-con">
			<view class="v-desc" v-for="(item, index) in trailer" :key="item.id">
				<view class="v-item"><video :src="item.trailer" :poster="item.cover" controls class="video"></video></view>
			</view>
		</view>

		<view class="like">
			<view class="l-con" v-for="(item, index) in likes" :key="item.id">
				<view class="l-img"><image class="img" :src="item.poster" mode=""></image></view>
				<view class="l-desc">
					<view class="name">{{ item.name }}</view>
					<view class="score"><uni-rate size="12" :max="5" :value="item.score"></uni-rate></view>
					<view class="info">{{ item.basicInfo }}</view>
					<view class="time">上映时间: {{ item.createTime }}</view>
				</view>
				<view class="zan">
					<view class="z-img"><image class="z-image" src="/static/icos/praise.png" mode=""></image></view>
					<view class="z-desc">赞一下</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import uniRate from '../../components/uni-rate/uni-rate.vue';
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
			likes: [] // 猜你喜欢
		};
	},
	onLoad() {
		this.getData();
		this.getMovieList();
		this.getTrailer();
		this.getLikes();
	},
	methods: {
		getData() {
			uni.request({
				url: `${this.$api}/carousel/list`,
				method: 'POST',
				data: {
					qq: '7929290'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res.data.status === 200) {
						this.banners = res.data.data;
					}
				}
			});
		},
		getMovieList() {
			uni.request({
				url: `${this.$api}/movie/hot`,
				method: 'POST',
				data: {
					qq: '7929290',
					type: 'superhero'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					this.movieList = res.data.data;
				}
			});
		},
		getTrailer() {
			uni.request({
				url: `${this.$api}/movie/hot`,
				method: 'POST',
				data: {
					qq: '7929290',
					type: 'trailer'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					this.trailer = res.data.data;
				}
			});
		},
		getLikes() {
			uni.request({
				url: `${this.$api}/guessULike`,
				method: 'POST',
				data: {
					qq: '7929290',
					type: 'trailer'
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					console.log(res.data.data);
					this.likes = res.data.data;
					this.likes.map(item => {
						item.createTime = this.$moment(item.createTime).format('YYYY-MM-DD');
					});
				}
			});
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
			font-size: 20upx;
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
.like {
	.l-con {
		display: flex;
		align-items: center;
		margin: 20px 0;
		.l-img {
			flex: 2;
			margin-left: 20upx;
			height: 360upx;
			.img {
				width: 260upx;
				height: 360upx;
				border-radius: 20upx;
			}
		}
		.zan {
			flex: 3;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			width: 20%;
			.z-img {
				display: flex;
				justify-content: center;
				width: 100%;
				height: 60upx;
				.z-image {
					width: 60upx;
					height: 60upx;
				}
			}
			.z-desc {
				font-size: 24upx;
			}
		}
		.l-desc {
			margin-left: 20upx;
			border-right: 4upx dashed #ccc;
			flex: 5;
			height: 360upx;
			display: flex;
			flex-direction: column;
			justify-content: center;
			padding: 0 20upx;
			.name {
				font-weight: 700;
			}
			.info {
				color: #ccc;
				font-size: 28upx;
				margin-top: 10upx;
			}
			.score {
				margin-top: 10upx;
			}
			.time {
				color: #ccc;
				font-size: 28upx;
				margin-top: 10upx;
			}
		}
	}
}
</style>
