<template>
	<view class="main">
		<image src="../../static/register_logo.svg" class="register_logo"></image>
		<input class="username" type="text" :value="username"  @input="change_password" />
		<input class="password" type="password" :value="password"  @input="change_password" >
		<input class="comfirm_password" type="password" :value="comfirm_password" @input="change_comfirm_password" />
		<button class="register" @tap="register()"><text class="register_text">注册</text></button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				username: "admin2",
				password: "admin",
				comfirm_password: "admin"
			}
		},
		methods: {
			register(e){
				console.log("点击了注册");
				let username = this.username
				let password = this.password
				let comfirm_password = this.comfirm_password
				console.log(username);
				console.log(password);
				console.log(comfirm_password)
				if(password==comfirm_password){
					console.log("密码一致");
					uni.request({
						url: '/api/jsb'+"/register",
						method: 'POST',
						header:{
							"Content-Type": "application/json",
						},
						data: {
							"username": this.username,
							"password": this.password
						},
						success(e) {
							console.log(e.data);
							if(e.data.code==200){
								//getApp().globalData.token = e.header.token
								uni.showToast({
									title: '注册成功'
								})
								uni.navigateTo({
									url:'../login/login'
								})
							}else{
								uni.showModal({
									title: '提示',
									content: '用户名已经注册',
									showCancel: false
								})
							}
						}
					})
				}else{
					uni.showToast({
						title: '密码不一致',
						icon: 'none'
					})
				}
			},
			//监听输入框赋值 
			change_comfirm_password(e){
				this.comfirm_password = e.target.value
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

	.register_logo {
		width: 258px;
		height: 258px;
		opacity: 1;
		margin: auto;
		margin-top: 14px;
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
		margin-top: 41px;
	}
	.password{
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
		margin-top: 7px;
		margin: auto;
	}
	.comfirm_password{
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
		margin-top: 7px;
	}
	.register{
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
		line-height: 20px;
		margin-top: 59px;
		display: flex;
		flex-direction: column;
	}
	.register_text{
		margin: auto;
	}
</style>
