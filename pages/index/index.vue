<template>
	<view class="container">
		<!-- 头部轮播 -->
		<view class="carousel-section">
			<!-- 标题栏和状态栏占位符 -->
			<view class="titleNview-placing"></view>
			<!-- 背景色区域 -->
			<view class="titleNview-background" :style="{backgroundColor:titleNViewBackground}"></view>
			<swiper autoplay="true" class="carousel" circular @change="swiperChange">
				<swiper-item v-for="(item, index) in carouselList" :key="index" class="carousel-item" @click="navToDetailPage({title: '轮播广告'})">
					<image :src="item.src" />
				</swiper-item>
			</swiper>
			<!-- 自定义swiper指示器 -->
			<view class="swiper-dots">
				<text class="num">{{swiperCurrent+1}}</text>
				<text class="sign">/</text>
				<text class="num">{{swiperLength}}</text>
			</view>
		</view>
		<!-- 分类 -->
		<view class="cate-section">
			<view class="cate-item">
				<image src="/static/temp/icon/icon-hl-001.png"></image>
				<a href="#homestay">民宿公寓</a>
			</view>
			<view class="cate-item">
				<image src="/static/temp/icon/icon-hl-002.png"></image>
				<a href="#hourRoom">钟点房间</a>
			</view>
			<view class="cate-item">
				<image src="/static/temp/icon/icon-hl-003.png"></image>
				<a href="#discount">酒店特惠</a>
			</view>
			<view class="cate-item">
				<image src="/static/temp/icon/icon-hl-004.png"></image>
				<text>机票车票</text>
			</view>
		</view>
		
		<!-- 订酒店 -->
		<view class="body">
			<view class="header">
				<view  @tap="switch1(0)" :class="current === 0 ? 'active left':'left'">国内酒店</view>
				<view  @tap="switch1(1)" :class="current === 1 ? 'active right':'right'">国际酒店</view>
			</view>
			<view v-if="current === 0" class="box1">
				<view class="location">
					<picker @change="bindPickerChangeLocation" :value="locationIndex" :range="array" >
							 {{array[locationIndex] ? '选择地点:  ' + array[locationIndex]:'选择地点:'}}
					</picker>
				</view>

				<view class="date">
					<picker  mode="date" @change="bindDateChange" >
					        {{startDate ? '入住时间:  ' + startDate:'入住时间:'}}
					</picker>
				</view>
				<view class="date">
					<picker  mode="date" @change="bindDateChange1" >
					        {{endDate ? '离开时间:  ' + endDate:'离开时间:'}}
					</picker>
				</view>
				<view @tap="search" class="search">查询酒店</view>
			</view>
			<view v-if="current === 1" class="box2">
				<view class="location">
						<picker @change="bindPickerChangeLocation" :value="locationIndex1" :range="array1" >
								 {{array1[locationIndex1] ? '选择地点:  ' + array1[locationIndex1]:'选择地点:'}}
						</picker>
					</view>
				
					<view class="date">
						<picker  mode="date" @change="bindDateChange" >
						        {{startDate ? '入住时间:  ' + startDate:'入住时间:'}}
						</picker>
					</view>
					<view class="date">
						<picker  mode="date" @change="bindDateChange1" >
						        {{endDate ? '离开时间:  ' + endDate:'离开时间:'}}
						</picker>
					</view>
				<view  @tap="search" class="search">查询酒店</view>
			</view>
		</view>
		
		<!-- 猜你喜欢 -->
		<view class="f-header m-t">
			<image src="/static/temp/h1.png"></image>
			<view class="tit-box">
				<text class="tit">猜你喜欢</text>
				<text class="tit2">Guess You Like It</text>
			</view>
			<text class="yticon icon-you"></text>
		</view>
		
		<view class="guess-section">
			<view 
				v-for="(item, index) in recommendList" :key="index"
				class="guess-item"
				@click="navToDetailPage(item)"
			>
				<view class="image-wrapper">
					<image :src="item.banner" mode="aspectFill"></image>
				</view>
				<text class="title clamp">{{item.name}}</text>
				<text class="price">￥{{item.price}}</text>
			</view>
		</view>
		
		<!-- 秒杀楼层 -->
<!-- 		<view class="seckill-section m-t">
			<view class="s-header">
				<image class="s-img" src="/static/temp/secskill-img.jpg" mode="widthFix"></image>
				<text class="tip">8点场</text>
				<text class="hour timer">07</text>
				<text class="minute timer">13</text>
				<text class="second timer">55</text>
				<text class="yticon icon-you"></text>
			</view>
			<scroll-view class="floor-list" scroll-x>
				<view class="scoll-wrapper">
					<view 
						v-for="(item, index) in goodsList" :key="index"
						class="floor-item"
						@click="navToDetailPage(item)"
					>
						<image :src="item.image" mode="aspectFill"></image>
						<text class="title clamp">{{item.title}}</text>
						<text class="price">￥{{item.price}}</text>
					</view>
				</view>
			</scroll-view>
		</view>
		 -->
		
		
		
		<!-- 分类推荐楼层 -->
		<view class="f-header m-t">
			<image src="/static/temp/h1.png"></image>
			<view class="tit-box">
				<text class="tit">分类精选</text>
				<text class="tit2">Competitive Products For You</text>
			</view>
			<text class="yticon icon-you"></text>
		</view>
		<view id="homestay" class="hot-floor">
			<view class="floor-img-box">
				<image class="floor-img" src="https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=3542922432,1408635103&fm=26&gp=0.jpg" mode="scaleToFill"></image>
			</view>
			<scroll-view class="floor-list" scroll-x>
				<view class="scoll-wrapper">
					<view 
						v-for="(item, index) in classList.homestay" :key="index"
						class="floor-item"
						@click="navToDetailPage(item)"
					>
						<image :src="item.banner" mode="aspectFill"></image>
						<text class="title clamp">{{item.name}}</text>
						<text class="price">￥{{item.price}}</text>
					</view>
					<view class="more">
						<text>查看全部</text>
						<text>More+</text>
					</view>
				</view>
			</scroll-view>
		</view>
		<view id="hourRoom" class="hot-floor">
			<view class="floor-img-box">
				<image class="floor-img" src="https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=3821067810,1375345716&fm=26&gp=0.jpg" mode="scaleToFill"></image>
			</view>
			<scroll-view class="floor-list" scroll-x>
				<view class="scoll-wrapper">
					<view 
						v-for="(item, index) in classList.hourRoom" :key="index"
						class="floor-item"
						@click="navToDetailPage(item)"
					>
						<image :src="item.banner" mode="aspectFill"></image>
						<text class="title clamp">{{item.name}}</text>
						<text class="price">￥{{item.price}}</text>
					</view>
					<view class="more">
						<text>查看全部</text>
						<text>More+</text>
					</view>
				</view>
			</scroll-view>
		</view>
		<view id="discount" class="hot-floor">
			<view class="floor-img-box">
				<image class="floor-img" src="https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3858741218,3141672019&fm=26&gp=0.jpg" mode="scaleToFill"></image>
			</view>
			<scroll-view class="floor-list" scroll-x>
				<view class="scoll-wrapper">
					<view 
						v-for="(item, index) in classList.discount" :key="index"
						class="floor-item"
						@click="navToDetailPage(item)"
					>
						<image :src="item.banner" mode="aspectFill"></image>
						<text class="title clamp">{{item.name}}</text>
						<text class="price">￥{{item.price}}</text>
					</view>
					<view class="more">
						<text>查看全部</text>
						<text>More+</text>
					</view>
				</view>
			</scroll-view>
		</view>

		

	</view>
</template>

<script>
	// import jq from '../../jquery.min.js'
	import {
	    mapMutations  
	} from 'vuex';
	export default {
            // 设置日期
		data() {
			return {
				titleNViewBackground: '',
				swiperCurrent: 0,
				swiperLength: 0,
				carouselList: [],
				goodsList: [],
				// 当前选中的酒店
				current: 0, //0-国内 1-国际
				locationIndex: 0,
				locationIndex1: 0,
				array: ['北京', '上海', '广州', '海南', '香港'],
				array1: ['伦敦', '纽约', '东京'],
				startDate: '', 
				endDate: '', 
				recommendList: [],
				classList:{
					homestay:[],
					hourRoom:[],
					discount:[]
				},
			};
		},

		onLoad() {
			this.loadData();
		},
		methods: {
			...mapMutations(['getTime']),
			/**
			 * 请求静态数据只是为了代码不那么乱
			 * 分次请求未作整合
			 */
			async loadData() {
				// 获取轮播图
				let carouselList = await this.$api.json('carouselList');
				this.titleNViewBackground = carouselList[0].background;
				this.swiperLength = carouselList.length;
				this.carouselList = carouselList;
				// 获取所有的酒店列表
				uni.request({
				　　url:"http://127.0.0.1:3001/api/hotelList",
				　　method:"GET",
				　　success:(res)=> {
					this.goodsList = res.data || [];
					this.goodsList.forEach((item)=>{
						if(item.type === 'hourRoom'){
							this.classList.hourRoom.push(item)
						}
						if(item.type === 'discount'){
							this.classList.discount.push(item)
						}
						if(item.type === 'homestay'){
							this.classList.homestay.push(item)
						}
						if(item.like){
							this.recommendList.push(item)
						}
					})
				　　}
				　})
			},
			//轮播图切换修改背景色
			swiperChange(e) {
				const index = e.detail.current;
				this.swiperCurrent = index;
				this.titleNViewBackground = this.carouselList[index].background;
			},
			// 切换国内国际
			switch1(num){
				if(num === 0){
					this.current = 0
				}else{
					this.current = 1
				}
			},
			// 选择地点
			bindPickerChangeLocation (e){
				this.locationIndex = e.detail.value
			},
			// 选择入住日期
			bindDateChange (e) {
				this.startDate = e.detail.value
			},
			// 选择离开日期
			bindDateChange1 (e) {
				this.endDate = e.detail.value
			},
			showData (result) {
			     var data = JSON.stringify(result); //json对象转成字符串
			     console.log('111',data);
			 },
			// 查询酒店
			search () {
				var endTime = new Date(this.endDate).getTime() / 1000 - parseInt(new Date(this.startDate).getTime() / 1000);
				var timeDay = parseInt(endTime / 60 / 60 / 24); 
				let obj = {}
				obj.endDate = this.endDate
				obj.startDate = this.startDate
				obj.dayNum = timeDay
				console.log('aaaa',obj)
				this.getTime(obj);
				uni.navigateTo({
					url: `/pages/product/list?city=${this.array[this.locationIndex]}`
				})
			},
			//详情页
			navToDetailPage(item) {
				//测试数据没有写id，用title代替
				let id = item.id;
				uni.navigateTo({
					url: `/pages/product/product?id=${id}`
				})
			},
		},
	}
</script>

<style lang="scss">
	page{
		.cate-section{
			position:relative;
			z-index:5;
			background-color: rgb(121, 199, 255);
			margin-top:-20upx;
		}
		.carousel-section{
			padding: 0;
			.titleNview-placing {
				padding-top: 0;
				height: 0;
			}
			.carousel{
				.carousel-item{
					padding: 0;
				}
			}
			.swiper-dots{
				left:45upx;
				bottom:40upx;
			}
		}
	}
	/* #endif */
	
	
	page {
		background: #f5f5f5;
	}
	.m-t{
		margin-top: 16upx;
	}
	/* 头部 轮播图 */
	.carousel-section {
		position: relative;

		.titleNview-placing {
			height: var(--status-bar-height);
			padding-top: 44px;
			box-sizing: content-box;
		}

		.titleNview-background {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 426upx;
			transition: .4s;
		}
	}
	.carousel {
		width: 100%;
		height: 426upx;

		.carousel-item {
			width: 100%;
			height: 100%;
			overflow: hidden;
			color: #ffffff;
		}

		image {
			width: 100%;
			height: 100%;
			border-radius: 10upx;
		}
	}
	.swiper-dots {
		display: flex;
		position: absolute;
		left: 60upx;
		bottom: 15upx;
		width: 72upx;
		height: 36upx;
		background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAABkCAYAAADDhn8LAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTMyIDc5LjE1OTI4NCwgMjAxNi8wNC8xOS0xMzoxMzo0MCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OTk4MzlBNjE0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OTk4MzlBNjA0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6Q0E3RUNERkE0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6Q0E3RUNERkI0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4Gh5BPAAACTUlEQVR42uzcQW7jQAwFUdN306l1uWwNww5kqdsmm6/2MwtVCp8CosQtP9vg/2+/gY+DRAMBgqnjIp2PaCxCLLldpPARRIiFj1yBbMV+cHZh9PURRLQNhY8kgWyL/WDtwujjI8hoE8rKLqb5CDJaRMJHokC6yKgSCR9JAukmokIknCQJpLOIrJFwMsBJELFcKHwM9BFkLBMKFxNcBCHlQ+FhoocgpVwwnv0Xn30QBJGMC0QcaBVJiAMiec/dcwKuL4j1QMsVCXFAJE4s4NQA3K/8Y6DzO4g40P7UcmIBJxbEesCKWBDg8wWxHrAiFgT4fEGsB/CwIhYE+AeBAAdPLOcV8HRmWRDAiQVcO7GcV8CLM8uCAE4sQCDAlHcQ7x+ABQEEAggEEAggEEAggEAAgQACASAQQCCAQACBAAIBBAIIBBAIIBBAIABe4e9iAe/xd7EAJxYgEGDeO4j3EODp/cOCAE4sYMyJ5cwCHs4rCwI4sYBxJ5YzC84rCwKcXxArAuthQYDzC2JF0H49LAhwYUGsCFqvx5EF2T07dMaJBetx4cRyaqFtHJ8EIhK0i8OJBQxcECuCVutxJhCRoE0cZwMRyRcFefa/ffZBVPogePihhyCnbBhcfMFFEFM+DD4m+ghSlgmDkwlOgpAl4+BkkJMgZdk4+EgaSCcpVX7bmY9kgXQQU+1TgE0c+QJZUUz1b2T4SBbIKmJW+3iMj2SBVBWz+leVfCQLpIqYbp8b85EskIxyfIOfK5Sf+wiCRJEsllQ+oqEkQfBxmD8BBgA5hVjXyrBNUQAAAABJRU5ErkJggg==);
		background-size: 100% 100%;

		.num {
			width: 36upx;
			height: 36upx;
			border-radius: 50px;
			font-size: 24upx;
			color: #fff;
			text-align: center;
			line-height: 36upx;
		}

		.sign {
			position: absolute;
			top: 0;
			left: 50%;
			line-height: 36upx;
			font-size: 12upx;
			color: #fff;
			transform: translateX(-50%);
		}
	}
	/* 分类 */
	.cate-section {
		display: flex;
		justify-content: space-around;
		align-items: center;
		flex-wrap:wrap;
		padding: 30upx 22upx; 
		background: #fff;
		.cate-item {
			display: flex;
			flex-direction: column;
			align-items: center;
			font-size: $font-sm + 2upx;
			color: #fff;
		}
		/* 原图标颜色太深,不想改图了,所以加了透明度 */
		image {
			width: 70upx;
			height: 70upx;
			margin-bottom: 14upx;
		}
	}
	/* 订酒店 */ 
	.body{
		width: 640upx;
		height: 590upx;
		background-color: #ffffff;
		margin: 20upx auto;
		border-radius: 16upx;
		.header{
			height: 80upx;
			display: flex;
			.left,.right{
				width: 50%;
				height: 80upx;
				text-align: center;
				line-height: 80upx;
				color: #585858;
				font-size: 32upx;
				background-color: #909399;
			}
			.active{
				background-color: #ffffff;
				color: #4499ff;
			}
		}
		.box1,.box2{
			height: 510upx;
			padding: 0 30upx 30upx 30upx;
			.location,.date,.search{
				height: 100upx;
				width: 580upx;
				border-bottom: 1px solid #EEEEEE;
				line-height: 100upx;
				color: #000;
				font-size: 32upx;
				font-weight: 500;
			}	
			.date{
				color: #333;
				font-weight:400;
			}
			.search{
				margin-top: 50upx;
				height: 88upx;
				border-radius: 16upx;
				background-color: #4499ff;
				color: #fff;
				text-align: center;
				line-height: 88upx;
			}
		}
	}
	/* 秒杀专区 */
	.seckill-section{
		padding: 4upx 30upx 24upx;
		background: #fff;
		.s-header{
			display:flex;
			align-items:center;
			height: 92upx;
			line-height: 1;
			.s-img{
				width: 140upx;
				height: 30upx;
			}
			.tip{
				font-size: $font-base;
				color: $font-color-light;
				margin: 0 20upx 0 40upx;
			}
			.timer{
				display:inline-block;
				width: 40upx;
				height: 36upx;
				text-align:center;
				line-height: 36upx;
				margin-right: 14upx;
				font-size: $font-sm+2upx;
				color: #fff;
				border-radius: 2px;
				background: rgba(0,0,0,.8);
			}
			.icon-you{
				font-size: $font-lg;
				color: $font-color-light;
				flex: 1;
				text-align: right;
			}
		}
		.floor-list{
			white-space: nowrap;
		}
		.scoll-wrapper{
			display:flex;
			align-items: flex-start;
		}
		.floor-item{
			width: 150upx;
			margin-right: 20upx;
			font-size: $font-sm+2upx;
			color: $font-color-dark;
			line-height: 1.8;
			image{
				width: 150upx;
				height: 150upx;
				border-radius: 6upx;
			}
			.price{
				color: $uni-color-primary;
			}
		}
	}
	
	.f-header{
		display:flex;
		align-items:center;
		height: 140upx;
		padding: 6upx 30upx 8upx;
		background: #fff;
		image{
			flex-shrink: 0;
			width: 80upx;
			height: 80upx;
			margin-right: 20upx;
		}
		.tit-box{
			flex: 1;
			display: flex;
			flex-direction: column;
		}
		.tit{
			font-size: $font-lg +2upx;
			color: #font-color-dark;
			line-height: 1.3;
		}
		.tit2{
			font-size: $font-sm;
			color: $font-color-light;
		}
		.icon-you{
			font-size: $font-lg +2upx;
			color: $font-color-light;
		}
	}
	/* 团购楼层 */
	.group-section{
		background: #fff;
		.g-swiper{
			height: 650upx;
			padding-bottom: 30upx;
		}
		.g-swiper-item{
			width: 100%;
			padding: 0 30upx;
			display:flex;
		}
		image{
			width: 100%;
			height: 460upx;
			border-radius: 4px;
		}
		.g-item{
			display:flex;
			flex-direction: column;
			overflow:hidden;
		}
		.left{
			flex: 1.2;
			margin-right: 24upx;
			.t-box{
				padding-top: 20upx;
			}
		}
		.right{
			flex: 0.8;
			flex-direction: column-reverse;
			.t-box{
				padding-bottom: 20upx;
			}
		}
		.t-box{
			height: 160upx;
			font-size: $font-base+2upx;
			color: $font-color-dark;
			line-height: 1.6;
		}
		.price{
			color:$uni-color-primary;
		}
		.m-price{
			font-size: $font-sm+2upx;
			text-decoration: line-through;
			color: $font-color-light;
			margin-left: 8upx;
		}
		.pro-box{
			display:flex;
			align-items:center;
			margin-top: 10upx;
			font-size: $font-sm;
			color: $font-base;
			padding-right: 10upx;
		}
		.progress-box{
			flex: 1;
			border-radius: 10px;
			overflow: hidden;
			margin-right: 8upx;
		}
	}
	/* 分类推荐楼层 */
	.hot-floor{
		width: 100%;
		overflow: hidden;
		margin-bottom: 20upx;
		.floor-img-box{
			width: 100%;
			height:320upx;
			position:relative;
			&:after{
				content: '';
				position:absolute;
				left: 0;
				top: 0;
				width: 100%;
				height: 100%;
				background: linear-gradient(rgba(255,255,255,.06) 30%, #f8f8f8);
			}
		}
		.floor-img{
			width: 100%;
			height: 100%;
		}
		.floor-list{
			white-space: nowrap;
			padding: 20upx;
			padding-right: 50upx;
			border-radius: 6upx;
			margin-top:-140upx;
			margin-left: 30upx;
			background: #fff;
			box-shadow: 1px 1px 5px rgba(0,0,0,.2);
			position: relative;
			z-index: 1;
		}
		.scoll-wrapper{
			display:flex;
			align-items: flex-start;
		}
		.floor-item{
			width: 180upx;
			margin-right: 20upx;
			font-size: $font-sm+2upx;
			color: $font-color-dark;
			line-height: 1.8;
			image{
				width: 180upx;
				height: 180upx;
				border-radius: 6upx;
			}
			.price{
				color: $uni-color-primary;
			}
		}
		.more{
			display:flex;
			align-items: center;
			justify-content: center;
			flex-direction: column;
			flex-shrink: 0;
			width: 180upx;
			height: 180upx;
			border-radius: 6upx;
			background: #f3f3f3;
			font-size: $font-base;
			color: $font-color-light;
			text:first-child{
				margin-bottom: 4upx;
			}
		}
	}
	/* 猜你喜欢 */
	.guess-section{
		display:flex;
		flex-wrap:wrap;
		padding: 0 30upx;
		background: #fff;
		.guess-item{
			display:flex;
			flex-direction: column;
			width: 48%;
			padding-bottom: 40upx;
			&:nth-child(2n+1){
				margin-right: 4%;
			}
		}
		.image-wrapper{
			width: 100%;
			height: 330upx;
			border-radius: 3px;
			overflow: hidden;
			image{
				width: 100%;
				height: 100%;
				opacity: 1;
			}
		}
		.title{
			font-size: $font-lg;
			color: $font-color-dark;
			line-height: 80upx;
		}
		.price{
			font-size: $font-lg;
			color: $uni-color-primary;
			line-height: 1;
		}
	}
	

</style>
