<template>
	<view class="container">
		<!-- 姓名输入 -->
		<view class="header_contain">
			<view class="left">
				<view class="form-item" style="border-bottom: 1px solid #2C405A;">
					<text>姓名:</text><text>{{name}}</text>
				
				</view>
				<view class="form-item" style="border-bottom: 1px solid #2C405A;">
					<text>性别:</text><text>{{sex}}</text>
				</view>
				<!-- 年龄输入 -->
				<view class="form-item" style="border-bottom: 1px solid #2C405A;">
					<text>年龄:</text><text>{{age}}</text>
				</view>
				<view class="form-item" style="border-bottom: 1px solid #2C405A;">
					<text>电话:</text><text>{{phonenumber}}</text>
				</view>
			</view>
			<view class="right">
				<image  :src="avator" style="width: 100%;"/>
			</view>
		</view>



		<!-- 文件上传 -->
		<view class="form-item">
			<text>详情描述:</text>
			<input type="text" v-model="detail" placeholder="请输入描述" />
			<text>上传材料:</text>
			<button @click="chooseFile">选择文件</button>
			<view v-if="fileName" class="file-name">{{ fileName }}</view>
			<view v-if="fileName" @click="shanchu" style=" color:#808080;width: 10%;border-radius: 7px;">删除</view>
			<view v-if="filePath" class="image-preview">
				<image :src="filePath" class="preview-img" />
			</view>
		</view>	

		<swiper v-if="file_list.length" class="swiper" :indicator-dots="true" @change="swiperSwitch">
			<swiper-item v-for="(item, index) in file_list" :index="index" :key="index">
				<view class="grid-item-box" style="background-color: #fff;">
					<image  @click="preview(index)" :src="item.filePath" class="preview-img" />
					<text class="text">{{item.fileName}}</text>
				</view>
			</swiper-item>
		</swiper>
		<view v-if= "file_list.length">
			<text style="color: #007AFF;" @click="cheack">查看详情|</text>
			<text style="color: #DD524D;" @click="delect">删除</text>
			</view>
		<view  class="fade-view" :class="{ hidden: !check }">
			<view class="form-item">
				<text>概况描述:</text>
				<view class="text-box">{{show_detail}}</view> 
			</view>
			<view class="form-item">
				<text>模型分析:</text>
				<view class="text-box">{{show_model_detail}}</view>
			</view>
			<view style="height: 100rpx;"></view>
		</view>
		<!-- 提交按钮 -->
		<button class="submit-btn" @click="submitForm">提交</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userid: this.$route.query.userid || '',
				phonenumber: this.$route.query.phonenumber || '',
				name: this.$route.query.name || '', // 姓名
				sex: this.$route.query.sex || '',
				age: this.$route.query.age || '', // 年龄
				avator: this.$route.query.filePath || '', // 年龄
				detail: '',
				file_list: [],
				imageList:[],
				fileName: '', // 上传的文件名
				filePath: '', // 上传的文件路径
				check:false,
				nowindex: 0,
				show_detail: '',
				show_model_detail: '',
			};
		},
		async onShow() {
			await this.initFn(this.userid)		
		},
		methods: {
			initFn(){
				uni.showLoading({
					title: '加载中...', // 可自定义加载框的提示文本
					mask: false // 设置为 `true` 时，会显示一个遮罩层，防止用户进行其他操作
				});
				setTimeout(() => {
					uni.hideLoading()
				}, 5000)
				uni.request({
					method: 'POST',
					url: 'http://192.168.2.59:3000/first/imageload', //仅为示例，并非真实接口地址。
					data: {
						userid: this.userid
					},
					header: {
						'content-type': 'application/json', //自定义请求头信息
					},
					success: (res) => {
						let list = []
						let imagelist = []
						res.data.forEach((item) => {
							imagelist.push(
							"http://192.168.2.59:3000/" + item.filePath
							)
							list.push({
								...item,
								filePath: "http://192.168.2.59:3000/" + item.filePath
							})
						})
						this.imageList = imagelist
						this.file_list = list || []
						console.log(this.file_list)
						this.show_detail = list[0]?.detail || ''
						this.show_model_detail = list[0]?.ai_content || ''
						uni.hideLoading()
					}
				})
			},
			shanchu() {
				this.fileName = '', // 上传的文件名
					this.filePath = ''
			},
			preview(index) {
			      uni.previewImage({
			        current: this.imageList[index], // 当前预览的图片
			        urls: this.imageList // 图片数组
			      });
			    },
			swiperSwitch(e){
				  this.nowindex = e.detail.current
				  this.check = false
				  this.show_detail = this.file_list[this.nowindex].detail
				  this.show_model_detail = this.file_list[this.nowindex].modeldetial || ''
			},
			cheack(){
				this.check = true
			},
			delect(){
				let id = this.file_list[this.nowindex].id
				console.log(id,'idddddddddddddddd')
				uni.request({
					method: 'POST',
					url: 'http://192.168.2.59:3000/first/imagedelect', //仅为示例，并非真实接口地址。
					data: {
						id: id
					},
					header: {
						'content-type': 'application/json', //自定义请求头信息
					},
					success: (res) => {
						this.initFn()
						uni.showToast({
							  title: '删除成功',
							  icon: 'success', // 可选：'success'、'loading'、'none'
							  duration: 1000   // 显示时长，单位毫秒
						})
					},
					fail: () => {
						uni.showToast({
							  title: '删除失败',
							  icon: 'none', // 可选：'success'、'loading'、'none'
							  duration: 1000   // 显示时长，单位毫秒
						})
					}
				})
			},
			// 选择文件
			chooseFile() {
				uni.chooseImage({
					success: (res) => {
						console.log(res)
						this.fileName = res.tempFiles[0].name; // 获取文件名
						this.filePath = res.tempFilePaths[0]; // 获取文件路径
					}
				});
			},
			formatDate(date) {
				const pad = n => (n < 10 ? '0' + n : n)
				const y = date.getFullYear()
				const m = pad(date.getMonth() + 1)
				const d = pad(date.getDate())
				const h = pad(date.getHours())
				const mi = pad(date.getMinutes())
				const s = pad(date.getSeconds())
				return `${y}-${m}-${d} ${h}:${mi}:${s}`
			},
			// 提交表单
			submitForm() {
				if (!this.detail ||
					!this.filePath) {
					uni.showToast({
						title: '请填写详情并上传图片',
						icon: 'none'
					});
					return;
				}
				uni.showLoading({
					title: '上传中...', // 可自定义加载框的提示文本
					mask: true // 设置为 `true` 时，会显示一个遮罩层，防止用户进行其他操作
				});
				setTimeout(() => {
					uni.hideLoading()
				}, 60000)
				const now = new Date()
				// 使用 uni.uploadFile 上传文件
				uni.uploadFile({
					url: 'http://192.168.2.59:3000/first/imagesave', // 后端上传接口
					filePath: this.filePath,
					name: 'filePath',
					formData: {
						userid: this.userid,
						date: this.formatDate(now),
						detail: this.detail,
						fileName: this.fileName
					},
					success:(res)=> {
						const data = JSON.parse(res.data);
						if (data.success) {
							uni.hideLoading()
							uni.showToast({
								title: '提交成功',
								icon: 'success'
							})
							this.initFn(this.userid)
							this.shanchu()
							this.detail = ''
						} else {
							uni.hideLoading()
							uni.showToast({
								title: '提交失败',
								icon: 'none'
							});
						}
					},
					fail() {
						uni.hideLoading()
						uni.showToast({
							title: '上传失败',
							icon: 'none'
						});
					}
				});
			}
		}
	};
</script>

<style scoped>
	.container {
		/* padding: 20px; */
	}
	.header_contain{
		width: 100%;
		display: flex;
	}
	.left{
		flex:6;
	}
	.right{
		flex:4;
		margin: 5px;
		height: 80%;
		border-radius: 16rpx;
	}
	.form-item {
		margin-bottom: 15px;
	}

	input {
		width: 94%;
		padding: 10px;
		border: 1px solid #ccc;
		border-radius: 5px;
	}

	button {
		padding: 10px;
		background-color: #007aff;
		color: white;
		border: none;
		border-radius: 5px;
		font-size: 16px;
	}

	.file-name {
		margin-top: 5px;
		font-size: 14px;
		color: #888;
	}

	.submit-btn {
		position: fixed;
		bottom: 0px;
		width: 100%;
		/* padding: 12px; */
		background-color: #4caf50;
		color: white;
		border: none;
		border-radius: 5px;
		font-size: 16px;
	}

	.image-preview {
		left: 50%;
		transform: translateX(7%);
	}

	.grid-dynamic-box {
		margin-bottom: 15px;
	}

	.grid-item-box {
		flex: 1;
		// position: relative;
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 15px 0;
		
	}

	.grid-dot {
		position: absolute;
		top: 5px;
		right: 15px;
	}
	.text-box {
	  word-break: break-word; /* 强制长单词换行 */
	  white-space: normal;    /* 允许换行 */
	  overflow-wrap: break-word; /* 防止长串不换行 */
	}
	.fade-view {
	  transition: opacity 0.5s ease, transform 0.5s ease;
	  opacity: 1;
	  transform: scale(1);
	}
	
	.hidden {
	  opacity: 0;
	  transform: scale(0.95); /* 可选的缩小动画 */
	  pointer-events: none; /* 防止点击 */
	}
</style>
