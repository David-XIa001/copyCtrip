<template>
	<view class="container">
		<!-- 顶部 -->
		<view class="top">
			<view class="hotelName">{{this.hotelInfo.name}}</view>
			<view class="time">
				<view class="startTime">入住:{{this.choiceTime.startDate}}</view>
				<view class="sum">共{{this.choiceTime.dayNum}}晚</view>
				<view class="endTime">离开:{{this.choiceTime.endDate}}</view>
			</view>
			<view class="roomName">{{this.room.name}}</view>
			<view class="tag">
				<text>{{this.room.area}}㎡</text>
				<text>{{this.room.isWindow == 1 ?'有窗':'无窗'}}</text>
				<text>{{this.room.isBreast == 1 ?'有早餐':'无早餐'}}</text>
			</view>
		</view>
		
		<!-- 入住信息 -->
		<view class="info">
			<view class="tip">
				<text>入住信息-</text>
				<text>姓名与证件号保持一致</text>
			</view>
			<view class="roomNum">
				<picker @change="bindPickerChangeRoomNum" :value="roomIndex" :range="roomArray" >
						 {{roomArray[roomIndex] ? '房间数量:  ' + roomArray[roomIndex]:'房间数量:'}}
				</picker>
			</view>
			<view class="name lable">
				<text>姓名:</text><input v-model="name" placeholder="请输入姓名"></input>
			</view>
			<view class="phone lable">
				<text>电话:</text><input v-model="phone" type="number" maxlength="11" placeholder="请输入电话号码"></input>
			</view>
			<view class="roomNum">
				<picker @change="bindPickerChangeTime" :value="timeIndex" :range="timeArray" >
						 {{timeArray[timeIndex] ? '预计到店:  ' + timeArray[timeIndex]:'预计到店:'}}
				</picker>
			</view>
		</view>
		
		<view class="order">
			<view class="title">重要提示:</view>
				<view class="info1">扣款说明</view>
				<view class="info2">该订单确认后不
				可被取消修改，若未入住将收取您全额房费。携程会根据您的付款方式进行预授权或扣除房费，订单需等酒店或供应商确认后生效，订单确认结果以携程短信或邮件通知为准，如订单不确认将解除预授权或全额退款至您的付款账户。附加服务费用将与房费同时扣除或返还。</view>
				<view class="info1">服务提供方:</view>
				<view class="info2">预订服务由携程旗下上海赫程国际旅行社有限公司及其分公司提供、住宿服务由酒店提供。</view>
		</view>
			
		<!-- 底部 -->
		<view class="footer">
			<view class="price-content">
				<text>实付款</text>
				<text class="price-tip">￥</text>
				<text class="price">{{this.room.price * this.roomNum * this.choiceTime.dayNum}}</text>
			</view>
			<text class="submit" @click="submit">提交订单</text>
		</view>
		
		<!-- 优惠券面板 -->

	</view>
</template>

<script>
	import { mapState } from 'vuex'; 
	export default {
		data() {
			return {
				roomIndex: 0,
				roomArray: [1, 2, 3, 4, 5],
				timeIndex: 0,
				timeArray: ['12:00', '14:00', "16:00", "18:00", "20:00","22:00"],
				hotelInfo:{},
				room:{},
				roomNum:1,
				name:'',
				phone:'',
			}
		},
		onLoad(option){
			uni.request({
			　　url:"http://127.0.0.1:3001/api/orderRoom",
			　　method:"GET",
				data: {
						id: option.id,
						hotelId: option.hotelId
					},
			　　success:(res)=> {
				this.hotelInfo = res.data.hotel
				this.room = res.data.room
				console.log('sss',res.data)
			　　}
			})
			//商品数据
			//let data = JSON.parse(option.data);
			//console.log(data);
		},
		computed: {
			...mapState(['choiceTime'])
		},
		methods: {
			// 选择房间数
			bindPickerChangeRoomNum (e){
				console.log('sss',this.choiceTime)
				this.roomIndex = e.detail.value
				this.roomNum = this.roomArray[this.roomIndex]
			},
			bindPickerChangeTime (e){
				this.timeIndex = e.detail.value
			},
			// 支付
			submit(){
				let data = {}
				data.hotelId = this.room.hotelId
				data.roomId = this.room.id
				data.status = '待支付'
				data.price = this.room.price * this.roomNum * this.choiceTime.dayNum
				data.startDate = this.choiceTime.startDate
				data.endDate = this.choiceTime.endDate
				data.dayNum = this.choiceTime.dayNum
				data.roomNum = this.roomNum
				data.phone = this.phone 
				data.people = this.name
				
				
				uni.request({
				　　url:"http://127.0.0.1:3001/api/creatOrder",
				　　method:"POST",
					data: {
							data: data
						},
				　　success:(res)=> {
					let id =''
					for (var i in res.data) {
						id = res.data[i]
					}
					if(res.data){
						uni.redirectTo({
							url: `/pages/money/pay?id=${id}`
						})
					}
				　　}
				})

			},
			stopPrevent(){}
		}
	}
</script>

<style lang="scss">
	page {
		background: $page-color-base;
		padding-bottom: 100upx;
	}
	.container{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		background: $page-color-base;
	}
	.top{
		margin-top: 20upx;
		height: 200upx;
		width: 670upx;
		border-radius: 8upx;
		display: flex;
		flex-direction: column;
		background-color: #4061A1;
		padding: 20upx;
		.hotelName{
			font-size: 28upx;
			color: #fff;
		}
		.time{
			margin-top: 10upx;
			font-size: 24upx;
			display: flex;
			justify-content: space-between;
		}
		.roomName{
			margin-top: 10upx;
			font-size: 26upx;
		}
		.tag{
			margin-top: 10upx;
			font-size: $font-lg - 2upx;
			color: #999999;
			text{
				margin-right:10upx;
			}
		}
	}
	.lable{
		display: flex;
		font-size: 26upx;
		height: 88upx;
		border-bottom: 1px solid #EEEEEE;
		align-items: center;
		input{
			margin-left: 60upx;
			font-size: 26upx;
		}
	}
	.info{
		margin-top: 20upx;
		height: 490upx;
		width: 670upx;
		background-color: #fff;
		padding: 20upx;
		.tip{
			font-size: 26upx;
			height: 88upx;
			line-height: 88upx;
			border-bottom: 1px solid #EEEEEE;
		}
		.roomNum{
			font-size: 26upx;
			height: 88upx;
			line-height: 88upx;
			border-bottom: 1px solid #EEEEEE;
		}
	}
	
	.order{
		margin-top: 20upx;
		width: 670upx;
		background-color: #fff;
		padding: 20upx;
		border-radius: 8upx;
		color: #333;
		.title{
			font-size: 32upx;
			font-weight: 600;
			height: 88upx;
			line-height: 88upx;
			border-bottom: 1px solid #EEEEEE;
		}
		.info1{
			font-size: 28upx;
			font-weight: 600;
			height: 60upx;
			line-height: 60upx;
		}
		.info2{
			font-size: 26upx;
			font-weight: 400;
			line-height: 36upx;
		}
		
	}
	.footer{
		position: fixed;
		left: 0;
		bottom: 0;
		z-index: 995;
		display: flex;
		align-items: center;
		width: 100%;
		height: 90upx;
		justify-content: space-between;
		font-size: 30upx;
		background-color: #fff;
		z-index: 998;
		color: $font-color-base;
		box-shadow: 0 -1px 5px rgba(0,0,0,.1);
		.price-content{
			padding-left: 30upx;
		}
		.price-tip{
			color: $base-color;
			margin-left: 8upx;
		}
		.price{
			font-size: 36upx;
			color: $base-color;
		}
		.submit{
			display:flex;
			align-items:center;
			justify-content: center;
			width: 280upx;
			height: 100%;
			color: #fff;
			font-size: 32upx;
			background-color: $base-color;
		}
	}
	

</style>
