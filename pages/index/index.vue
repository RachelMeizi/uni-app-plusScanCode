<template>
	<view class="content">
		<view class="text-area">
			<text class="title">{{title}}</text>
			<button type="small" @click="onScan">点击扫码</button>
			<view class="">
				<view class="title">扫码回调参数：{{value}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				textscan: '扫码',
				typescan: 'scan-listener',
				title: 'Hello-plusScan',
				value:''
			}
		},
		onLoad() {

		},
		onReady() {
			 uni.$on(this.typescan,(data)=>{
				 this.value=data.result
			 })
		},
		methods: {
			onScan(){
				console.log('44444444');
				
				uni.navigateTo({
					url: '/pages/plusScan/index?text=' + this.textscan + '&type=' + this.typescan,
				});
			},
			
		}
	}
</script>

<style>
.content{
	width: 100%;
	display: flex;
	place-content: center;
}
button{
	background-color: #DD524D;
	color: #FFFFFF;
}
</style>
