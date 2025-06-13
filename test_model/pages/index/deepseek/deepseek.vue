

<template>
	<view class="container">
		<view class="search-bar">
			<input type="text" style="background-color: #FFFFFF;" placeholder="姓名搜索" v-model="searchQuery" />
			<text style="margin-top: 10rpx;color: #555555;" type="primary" @click="initFn()">搜索</text>
		</view>

		<view v-if="list.length == 0" style="text-align: center;color: #8F8F94;">
			暂无数据
		</view>
		<!-- 列表 -->
		<view v-else class="list">
			<view v-for="(item, index) in list" :key="index" class="list-item"  @longpress="delect(item)">
				<image class="avatar" :src="item.filePath" />
				<view class="info">
					<text class="name">{{ item.name }}</text>
					<text class="details">|{{ item.sex }} | {{ item.age }}岁 </text>
					<text class="details" style="display: inline-block; overflow: hidden;"></text>
					<view class="details">联系电话:{{ item.phonenumber }}</view>
				</view>
				<view style="display: flex;flex-direction: column;">
					<button style="width: 200rpx;" @click="viewDetails(item)">查看详情</button>
					<button style="width: 200rpx;" @click="editPerson(item)" >修改个人</button> 
				</view>
				
				<!-- <button v-if="index === 0" @click="editPerson(item)" style="margin-left: 10rpx;">修改</button> -->
			</view>
		</view>
		<view class="blank"></view>
		<!-- <button v-if="searchQuery" class="button" type="primary" @click="initFn()">搜索</button> -->

		<button class="button" type="primary" @click="newChange()">新增病员</button>
		
		  <!-- 弹窗内容 -->
		  <view v-if="showDialog" class="dialog" style="text-align: center;">
			  <view >
				 <text class="dialog-title">弹窗</text>
				 <text class="dialog-content">是否删除该人员信息？</text> 
			  </view>
		  
			 <button @click="closeDialog" type="primary" style="margin-right: 20rpx;" size="mini">关闭</button>
		    <button @click="deleOk" type="warn" size="mini" style="margin-left: 20rpx;">确定</button>
		  </view>
		  
		</view>
	</view>
</template>

<script>
	import { BASE_URL } from '@/static/config.js';
	export default {

		data() {
			return {
				searchQuery: '', // 搜索关键字
				list: [],
				max_id: 0,
				showDialog: false,
				current: {}
			};
		},
		onShow() {
			this.initFn()
		},
		computed: {},
		methods: {
			// 初始请求
			initFn() {
				uni.showLoading({
					title: '加载中...', // 可自定义加载框的提示文本
					mask: false // 设置为 `true` 时，会显示一个遮罩层，防止用户进行其他操作
				});
				setTimeout(() => {
					uni.hideLoading()
				}, 5000)
				uni.request({
					method: 'POST',
					url: `${BASE_URL}first/search`, //仅为示例，并非真实接口地址。
					data: {
						username: this.searchQuery
					},
					header: {
						'content-type': 'application/json', //自定义请求头信息
						// 'access-control-allow-origin: "*"
					},
					success: (res) => {
						let list = []
						res.data.forEach((item) => {
							list.push({
								...item,
								filePath: `${BASE_URL}` + item.filePath
							})
						})
						this.list = list || []

						const maxScore = Math.max(...list.map(item => item.userid))
						if (this.max_id < maxScore) {
							this.max_id = maxScore
						}
						console.log(this.max_id)

						uni.hideLoading()
					}
				})
			},
			// 查看详情方法，传递选中的item到详情页
			viewDetails(item) {
				console.log(item)
				uni.navigateTo({
					url: `/pages/index/deepseek/detail?userid=${item.userid}&age=${item.age}&name=${item.name}&phonenumber=${item.phonenumber}&sex=${item.sex}&filePath=${item.filePath}`
				});
			},
			editPerson(item) {
			  uni.navigateTo({
				url: `/pages/index/deepseek/persondetail?userid=${item.userid}&age=${item.age}&name=${item.name}&phonenumber=${item.phonenumber}&sex=${item.sex}&filePath=${item.filePath}&fileName=${item.fileName}&mode=edit`
			  })
			},
			// 创建跳转新页面
			newChange() {
				let id = this.max_id + 1
				uni.navigateTo({
					url: `/pages/index/deepseek/persondetail?userid=${id}`
				});
			},

			delect(item) {
				console.log(item) 
				this.current = item
				this.showDialog = true
			},
			deleOk(){
				console.log(this.current.id,this.current.userid) 
				uni.showLoading({
					title: '加载中...', // 可自定义加载框的提示文本
					mask: false // 设置为 `true` 时，会显示一个遮罩层，防止用户进行其他操作
				});
				setTimeout(() => {
					uni.hideLoading()
				}, 5000)
				uni.request({
					method: 'POST',
					url: `${BASE_URL}first/delpersson`, //仅为示例，并非真实接口地址。
					data: {
						id:  this.current.id,
						userid: this.current.userid,
					},
					header: {
						'content-type': 'application/json', //自定义请求头信息
					},
					success: (res) => {
						console.log(res)
						this.showDialog = false
						this.initFn()
						uni.hideLoading()
					}
				})
			},
			closeDialog() {
			  this.showDialog = false
			}

		}
	};
</script>

<style scoped>
	.blank {
		height: 16rpx;
	}

	.warp {
		padding: 10px;
	}

	.button {
		width: 100%;
		position: fixed;
		left: 50%;
		transform: translateX(-50%);
		bottom: 0rpx;


	}

	.container {
		padding: 20px;
	}

	.search-bar {
		background-color: #FFFFFF;
		position: sticky;
		/* 使搜索框固定 */
		top: 80rpx;
		z-index: 10;
		/* 吸附到页面顶部 */
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
		/* 阴影效果，增加浮动感 */
		margin-bottom: 20px;
		display: flex;
		justify-content: center;
	}

	.search-bar input {
		width: 80%;
		padding: 8px;
		font-size: 16px;
		border-radius: 20px;
		border: 1px solid #ccc;
	}

	.list {
		display: flex;
		flex-direction: column;
		background-color: #d9d9d9;
		border-radius: 7rpx;
	}

	.list-item {
		display: flex;
		padding: 10px;
		border-bottom: 5px solid #f0f0f0;
		align-items: center;
	}

	.avatar {
		width: 50px;
		height: 50px;
		border-radius: 50%;
		margin-right: 15px;
	}

	.info {
		flex: 1;
		max-width: 300rpx;
	}

	.name {
		font-size: 18px;
		font-weight: bold;
	}

	.details {
		color: #888;
		font-size: 14px;
		overflow: hidden;
		white-space: nowrap;
	}


	.btn {
		background-color: #007aff;
		color: white;
		padding: 5px 10px;
		border-radius: 5px;
		font-size: 14px;
		margin-left: 10px;
	}

	/* 弹窗框 */
	.dialog {
	  position: fixed;
	  left: 50%;
	  top: 50%;
	  width: 70%;
	  transform: translate(-50%, -50%);
	  background-color: #fff;
	  border-radius: 20rpx;
	  padding: 40rpx 30rpx;
	  z-index: 1000;
	  box-shadow: 0 10rpx 30rpx rgba(0, 0, 0, 0.2);
	}
	
	.dialog-title {
	  font-size: 34rpx;
	  font-weight: bold;
	  margin-bottom: 20rpx;
	  display: block;
	  text-align: center;
	}
	
	.dialog-content {
	  font-size: 28rpx;
	  text-align: center;
	  margin-bottom: 30rpx;
	}
</style>
