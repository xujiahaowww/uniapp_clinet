<template>
	<view class="container">
		<!-- 头像上传 -->
		<view class="form-item">
			<text>头像:</text><text style="margin-left: 20rpx;color:#DD524D ;" v-if="filePath" @click="shanchu()">删除</text>
			<view class="avatar-wrapper" @click="chooseFile">
				<image class="avatar" :src="filePath " mode="aspectFill"></image>
			</view>
		</view>
		<!-- 姓名输入 -->
		<view class="form-item">
			<text>姓名:</text>
			<input v-model="name" placeholder="请输入姓名" />
		</view>
		<view class="form-item">
			<text>性别:</text>
			<input v-model="sex" placeholder="请输入性别" />
		</view>
		<view class="form-item">
			<text>电话:</text>
			<input type="number" v-model="phonenumber" placeholder="请输入电话" />
		</view>

		<!-- 年龄输入 -->
		<view class="form-item">
			<text>年龄:</text>
			<input type="number" v-model="age" placeholder="请输入年龄" />
		</view>

		<!-- 提交按钮 -->
		<button class="submit-btn" @click="submitForm">提交</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				filePath:'',
				userid: this.$route.query.userid || '',
				phonenumber: '',
				name: '', // 姓名
				sex: '',
				age: '', // 年龄
			};
		},
		async mounted() {
			// await this.initFn(this.userid)
		},
		methods: {
			// 初始请求
			shanchu() {
				this.fileName = '', // 上传的文件名
					this.filePath = ''
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

			// 提交表单
			submitForm() {
				if (!this.name ||
					!this.age ||
					!this.sex ||
					!this.phonenumber||
					!this.filePath
				) {
					uni.showToast({
						title: '请填写完整的表单',
						icon: 'none'
					});
					return;
				}
				uni.showLoading({
					title: '提交中...', // 可自定义加载框的提示文本
					mask: true // 设置为 `true` 时，会显示一个遮罩层，防止用户进行其他操作
				});
				setTimeout(() => {
					uni.hideLoading()
				}, 5000)
				// 使用 uni.uploadFile 上传文件
				uni.uploadFile({
					url: 'http://192.168.2.59:3000/first/savepersson', // 你的后端上传接口
					filePath: this.filePath,
					name: 'filePath', // 后端接收字段名
					formData: {
						userid: this.userid,
						phonenumber: this.phonenumber,
						name: this.name,
						age: this.age,
						sex: this.sex,
						fileName: this.fileName
					},
					success: res => {
						const data = JSON.parse(res.data);
						if (data.success) {
							uni.showToast({
								title: '提交成功'
							});
							setTimeout(()=>{
								uni.navigateTo({
									url: `/pages/index/deepseek/deepseek`
								})
							},2000)
						} else {
							uni.showToast({
								title: '提交失败',
								icon: 'none'
							});
						}
					},
					fail: err => {
						console.error(err);
						uni.showToast({
							title: '提交失败',
							icon: 'none'
						});
					}
				});
			}
		}
	};
</script>

<style scoped>
	.avatar-wrapper {
		width: 150rpx;
		height: 150rpx;
		border-radius: 50%;
		overflow: hidden;
		border: 2rpx solid #ccc;
	}

	.avatar {
		width: 100%;
		height: 100%;
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
</style>
