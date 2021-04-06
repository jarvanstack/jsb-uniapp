<template>
	<view class="main">
		<image class="login_logo" src="../../static/login_logo.svg"></image>

		<input :value="username" class="username" type="text" @input="chage_username"/>
		<input class="password" :value="password" type="password" @input="change_password" />
		<text class="to_register" @tap="to_register">注册</text>
		<button class="login" @tap="login"><text class="login_text">登录</text></button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				username: "admin",
				password: "admin"
			}
		},
		methods: {
			to_register(e){
				console.log("点击了注册");
				uni.navigateTo({
					url: '../register/register'
				})
			},
			login(e){
				console.log("点击了登录");
				uni.showToast({
					title:'正在登录',
					icon:'loading',
					duration: 2000
				})
				console.log(this.username);
				console.log(this.password);
				uni.request({
					url: '/api/jsb'+"/login",
					method: 'POST',
					header:{
						"Content-Type": "application/json",
					},
					data: {
						"username": this.username,
						"password": this.password
					},
					success(e) {
						if(e.data.code==200){
							//getApp().globalData.token = e.header.token
							uni.showToast({
								title: '登录成功'
							})
							let token = e.header.token;
							getApp().globalData.token = token
							getApp().globalData.username = this.username
							uni.navigateTo({
								url:'../index/index'
							})
						}else{
							uni.showModal({
								title: '提示',
								content: '账号或者密码错误',
								showCancel: false
							})
						}
					}
				})
				
			},
			change_password(e){
				this.password = e.target.value
			},
			chage_username(e){
				this.username = e.target.value
			}
		}
	}
</script>

<style>
	.main {
		opacity: 1;
		background: rgba(0, 0, 0, 0.00);
	}

	/* 布局 */
	.main {
		display: flex;
		flex-direction: column;
	}

	.login_logo {
		margin-top: 14px;
		width: 258px;
		height: 258px;
		opacity: 1;
		margin: auto;
	}

	.username {
		width: 209px;
		height: 46px;
		opacity: 1;
		background: #ffffff;
		border-bottom: 1px solid #797979;
		border-radius: 1px;
		font-size: 14px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: center;
		color: #333333;
		line-height: 0px;
		margin: auto;
		margin-top: 97rpx;
	}

	.password {
		width: 209px;
		height: 46px;
		opacity: 1;
		background: #ffffff;
		border-bottom: 1px solid #797979;
		border-radius: 1px;
		font-size: 14px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: center;
		color: #333333;
		line-height: 0px;
		margin: auto;
		margin-top: 7rpx;
	}

	.to_register {
		width: 28px;
		height: 20px;
		opacity: 1;
		font-size: 14px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: left;
		color: #005cff;
		line-height: 0px;
		margin-left: 65%;
		margin-top: 35rpx;
	}

	.login {
		width: 183px;
		height: 46px;
		opacity: 1;
		background: #5a8bbc;
		border-radius: 11px;
		font-size: 18px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: center;
		color: #d6eeff;
		margin: auto;
		display: flex;
		flex-direction: column;

	}
	/* fuck zhe 个文字居中 */
	.login_text{
		margin: auto;
	}
</style>
