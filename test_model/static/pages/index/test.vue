<template>
  <div>
    <input v-model="message" placeholder="输入一些内容">
    <p>你输入的内容是: {{ message }}</p>
	<!-- <Child :message = '你好'></Child> -->
	<button @click="upload">上传</button>
  </div>
</template>

<script>
import Child from './child.vue'

export default {

  data() {
    return {
      message: '',
	  
    }},
  methods:{
	 chuli:()=>{
	 	console.log('从父组件来的方法')
	 } ,
	 upload:()=>{
		 uni.chooseImage({
		 	success: (chooseImageRes) => {
		 		const tempFilePaths = chooseImageRes.tempFilePaths;
				const id = Math.ceil(Math.random()*50) 
		 		uni.uploadFile({
		 			url: ' http://192.168.2.59:3000/first/second_3', //仅为示例，非真实的接口地址
		 			filePath: tempFilePaths[0],
		 			name: 'img',
		 			formData: {
						'id': id,
		 				'user': 'test'
		 			},
		 			success: (uploadFileRes) => {
		 				console.log(uploadFileRes.data);
		 			}
		 		});
		 	}
		 });
	 }
  },
  provide() {
      return {
        callParentMethod: this.chuli, // 提供方法给后代组件使用
      };
    },
}


</script>
<style>
	
</style>