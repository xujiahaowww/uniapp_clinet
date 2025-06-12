<template>
<view>
	<div>{{message}}</div>
	<button @click="callParentMethod">123</button>
</view>
</template>

<script>
	export default{
		 props: ['message'],
		 inject:['callParentMethod'],
		 methods:{
			 // fn: this.callParentMethod
		 }
	}
</script>

<style>
</style>
