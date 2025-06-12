<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
		<Child message = '你好' fn = chuli></Child>
	</view>
</template>


<script>
	import Child from './child.vue'
	export default {
		components:{
			Child
		},
		data() {
			return {
				title: 'Hello'
			}
		},
		provide() {
		    return {
		      callParentMethod: this.chuli, // 提供方法给后代组件使用
		    };
		  },
		onLoad() {

		},
		methods: {
			chuli:()=>{
				console.log('从父组件来的方法')
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
