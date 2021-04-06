<template>
<view class="body">
	<view class="main">
		
		<scroll-view class="messages" scroll-y="true">
			<view  v-for="(message,index) in messages" :key="index">
				<view class="message">
					{{message.username}}:{{message.message}}
				</view>
			</view>
		</scroll-view>
	</view>
	<view class="fasongkuang">
		<input class="shurukuang" @input="shurukuang_change" @confirm="fasong" :value="shurukuang"/>
		<button class="send_button" @tap="fasong"><view class="send_button_text">发送</view></button>
	</view>
</view>
</template>

<script>
	export default {
		data() {
			return {
				shurukuang: '',
				messages: [
					{
						username: '系统',
						user_or_system: 0,
						message: "您好,这里是石头剪刀布游戏，开始游戏输入「jsb」"
					},
					{
						username: '系统',
						user_or_system: 0,
						message: "自动选择人机模式"
					}
				]
			}
		},
		onLoad() {
			console.log(getApp().globalData.token);
			console.log(getApp().globalData.username);
		},
		methods: {
			shurukuang_change(e){
				this.shurukuang = e.target.value
			},
			fasong(){
				let message = this.shurukuang
				//发送信息到信息框
				let newList = {
				   username:'用户',
				   user_or_system:0,
				   message: this.shurukuang
				 }
				let number = this.messages.push(newList)
				//清空输入框
				this.shurukuang = ""
				console.log(message);
				let  newMessage = {
						   username:'系统',
						   user_or_system:0,
						   message: ''
				}
				//发送信息到服务端
				uni.request({
					url: '/api/jsb'+"/send-message",
					method: 'POST',
					header:{
						"token": getApp().globalData.token,
					},
					data:{
						"message": message
					},
					success(e) {
						console.log(e.data.data);
						newMessage.message = e.data.data
					}
				})
				this.messages.push(newMessage)
			}
		}
	}
</script>

<style>

	.message{
		display: flex;
		font-size: 18px;
		line-height: auto;
	}
	.fasongkuang{
		display: flex;
		width: 414px;
		height: 64px;
		opacity: 1;
		background: #f5f6fb;
		position: fixed;
		bottom: 0px;
	}
	.shurukuang{
		margin-top: 8px;
		margin-left: 5%;
		width: 314px;
		height: 48px;
		opacity: 1;
		background: #ffffff;
		border: 1px solid #797979;
		border-radius: 15px;
		font-size: 18px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: left;
		color: #333333;
		line-height: 24px;
	}
	.send_button{
		display: flex;
		flex-direction: column;
		border-radius: 15px;
		margin-top: 8px;
		width: 60px;
		height: 48px;
		opacity: 1;
		background: #00c9fc;
		font-size: 16px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: center;
		color: #ffffff;
		line-height: 22px;
	}
	.send_button_text{
		margin: auto;
	}
</style>
