<template>
	<view class="warp">
		<view class="dev-area">
			<view class="dev-cart">
				<view class="">
					<view class="dev-name"> 温度 </view>
					<image class="dev-logo" src="../../static/temp.png" mode=""></image>
				</view>
				<view class="dev-data"> {{temperature}} ℃ </view>
			</view>

			<view class="dev-cart">
				<view class="">
					<view class="dev-name"> 湿度 </view>
					<image class="dev-logo" src="../../static/humi.png" mode=""></image>
				</view>
				<view class="dev-data"> {{humidity}} % </view>
			</view>

			<view class="dev-cart">
				<view class="">
					<view class="dev-name"> 小灯 </view>
					<image class="dev-logo" src="../../static/led.png" mode=""></image>
				</view>
				<switch :checked="led" @change="onLedSwitch" />
			</view>
		</view>
	</view>


</template>

<script>
	const {
		createCommonToken
	} = require('@/key.js')
	export default {
		data() {
			return {
				temperature: '',
				humidity: '',
				led: true,
				token: ''
			}
		},
		onLoad() {
			const params = {
				author_key: 'At2LT7FFhW9qWEq2TNQKZDsC6VBWIC9/vHy+K/Meu3ksm9BifSZuUpIxrZyTEV9g8VDKQhXm6L6f5l+1Vvgscw==',
				version: '2022-05-01',
				user_id: '380867'
			}

			// 创建一个token返回
			this.token = createCommonToken(params)
		},
		onShow() {
			this.fetchDevData();
			// 定时获取数据(3s)
			setInterval(() => {
				this.fetchDevData();
			}, 3000)
		},
		methods: {
			// 获取数据的api
			fetchDevData() {
				uni.request({
					url: 'https://iot-api.heclouds.com/thingmodel/query-device-property',
					method: 'GET',
					data: {
						product_id: '4300VXBSWr', // 产品id
						device_name: 'luo' // 设备名称
					},
					header: {
						'authorization': this.token //自定义请求头信息
					},
					success: (res) => {
						console.log(res.data);
						this.humidity = res.data.data[0].value;
						this.temperature = res.data.data[2].value;
						this.led = res.data.data[1].value === 'true';
					}
				});
			},
			onLedSwitch(event) {
				let ledVal = event.detail.value;
				uni.request({
					url: 'https://iot-api.heclouds.com/thingmodel/set-device-property',
					method: 'POST',
					data: {
						product_id: '4300VXBSWr', // 产品id
						device_name: 'luo', // 设备名称
						params: {
							"led": ledVal
						}
					},
					header: {
						'authorization': this.token //自定义请求头信息
					},
					success: (res) => {
						console.log('LED ' + (ledVal ? 'NO' : 'OFF') + '!');
					}
				});
			}
		}
	}
</script>

<style>
	.warp {
		padding: 30rpx;
	}
	
	.dev-area {
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
	}

	.dev-cart {
		height: 150rpx;
		width: 320rpx;
		border-radius: 30rpx;
		margin-top: 30rpx;
		display: flex;
		justify-content: space-around;
		align-items: center;
		box-shadow: 00 0 15rpx #ccc;
	}

	.dev-name {
		font-size: 20rpx;
		text-align: center;
		color: #6d6d6d;
	}

	.dev-logo {
		width: 70rpx;
		height: 70rpx;
		margin-top: 10rpx;
	}

	.dev-data {
		font-size: 50rpx;
		color: #6d6d6d;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>