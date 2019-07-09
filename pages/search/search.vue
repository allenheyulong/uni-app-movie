<template>
	<view>
		<view class="search"><m-search placeholder="请输入电影信息" @search="search($event)"></m-search></view>
		<view class="list">
			<view class="desc" v-for="(item, index) in movieList" :key="item.id">
				<view class="item" @click="goDetail(item, index)">
					<view class="img"><image :src="item.poster" :lazy-load="true"></image></view>
					<view class="name">{{ item.name }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import mSearch from '../../components/mehaotian-search/mehaotian-search.vue';
export default {
	components: {
		mSearch
	},
	data() {
		return {
			movieList: [],
			keywords: '',
			page: 1,
			pageSize: 12
		};
	},
	// 上拉刷新
	onReachBottom() {
		uni.showLoading({
			title: '加载中...'
		});
		this.page++;
		uni.request({
			url: `${this.$api}/search/list`,
			method: 'POST',
			data: {
				qq: '7929290',
				keywords: this.keywords,
				page: this.page,
				pageSize: this.pageSize
			},
			header: {
				'content-type': 'application/x-www-form-urlencoded'
			},
			success: res => {
				if (res) {
					uni.hideLoading();
					res.data.data.rows.map(item => {
						this.movieList.push(item);
					});
				}
			},
			fail: err => {
				console.log(err);
			}
		});
	},
	methods: {
		getList() {
			uni.showLoading({
				title: '加载中...'
			});
			uni.request({
				url: `${this.$api}/search/list`,
				method: 'POST',
				data: {
					qq: '7929290',
					keywords: this.keywords,
					page: this.page,
					pageSize: this.pageSize
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res) {
						uni.hideLoading();
						this.movieList = res.data.data.rows;
					}
				},
				fail: err => {
					console.log(err);
				}
			});
		},
		search(e) {
			this.keywords = e;
			this.page = 1;
			this.pageSize = 12;
			this.getList();
		},
		goDetail(item, index) {
			uni.navigateTo({
				url: '/pages/detail/detail?item=' + JSON.stringify(item)
			});
		}
	},
	onLoad() {
		this.getList();
	}
};
</script>

<style lang="scss">
.search {
	position: fixed;
	top: 0;
	z-index: 99;
}
.list {
	display: flex;
	align-items: center;
	flex-wrap: wrap;
	margin-top: 120upx;
	.desc {
		width: 33%;
		height: 360upx;
		.item {
			margin: 0 30upx;
			display: flex;
			flex-direction: column;
			justify-content: center;
			.img {
				width: 200upx;
				height: 260upx;
				image {
					width: 200upx;
					height: 260upx;
				}
			}
			.name {
				font-size: 28upx;
			}
		}
	}
}
</style>
