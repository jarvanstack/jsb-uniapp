<template>
	<view class="body">
		<view class="main">

			<scroll-view class="messages" scroll-y="true" :scroll-into-view="scroll_into_view" show-scrollbar="true"
				scroll-with-animation="true">
				<view v-for="(message,index) in messages" :key="index" :id="'aaa'+index">
					<view class="system_message" v-show="!message.is_user" :style="getHeight(message.message)">
						{{message.message}}
					</view>
					<view class="user_message_box" v-show="message.is_user">
						<view class="user_message" :style="getHeight(message.message)">
							{{message.message}}
						</view>
					</view>

				</view>
			</scroll-view>
			<view class="fasongkuang">
				<input class="shurukuang" @input="shurukuang_change" @confirm="fasong" :value="shurukuang" />
				<button class="send_button" @tap="fasong">
					<view class="send_button_text">发送</view>
				</button>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				height: {
					height: uni.getSystemInfoSync().screenHeight + 'px'
				},
				shurukuang: '',
				messages: [{
					username: '系统',
					is_user: false,
					message: '您好,欢迎来到石头剪刀布游戏'
				},
				{
					username: '系统',
					is_user: false,
					message: '1.新建房间输入「jsb」2.输入房间号进入「房间号」比如「13」3.开始对局输入「j」「s」「b」代表剪刀、石头、步 4.输入「dl」重新登录 5.输入「jl」查看对局记录'
				}],
				socketTask: null,
				// 确保websocket是打开状态
				is_open_socket: false,
				scroll_into_view: ''
			}
		},
		//每次连接时候检查是否连接socket没有连接就自动连接
		onShow() {
			console.log("index.onShow()");
			console.log("index.onShow()重新连接");
			this.connectSocketInit()
		},
		// 关闭websocket【必须在实例销毁之前关闭,否则会是underfined错误】
		beforeDestroy() {
			this.socketTask.close()
		},
		methods: {
			shurukuang_change(e) {
				this.shurukuang = e.target.value
			},
			getHeight(str) {
				let len = str.length;
				let hang = 1;

				while (len > 15) {
					len = len - 15;
					hang = hang + 1;
				}
				let width = 0;
				if (hang > 1) {
					width = 330;
				} else {
					width = len * 20;
				}
				let height = hang * 25;
				return {
					height: height + "px",
					width: width + 'px'
				};
			},
			fasong() {
				let message = this.shurukuang
				if (message == "dl") {
					uni.showModal({
						title: '去登录',
						content: '是否前往登录页面登录?',
						success(e) {
							if (e.confirm) {
								uni.navigateTo({
									url: '../login/login'
								})
							} else {
								uni.showToast({
									title: '取消',
									icon: 'none'
								})
							}
						}
					})

				} else if (message == "ws") {
					console.log("连接到ws");
					this.connectSocketInit()
				} else {
					//1.首先发送信息到用户框
					this.fasong_to_messages(message)
					//发送信息到后端服务器
					//如果websocket是连接的
					if (this.is_open_socket) {
						this.fasong_to_server(message)
					}
				}


			},
			fasong_to_server(message) {
				this.socketTask.send({
					data: message,
					async success(e) {
						console.log(e);
						console.log("发送到服务器成功");
					},
					async fail(e) {
						console.log(e)
						console.log("发送到服务器失败");
					}
				})
			},
			fasong_to_messages(message) {
				let newList = {
					username: '用户',
					is_user: true,
					message: message
				}
				this.messages.push(newList)
				//滚动到瞄点
				this.ne
				this.$nextTick(function() {
					this.scroll_into_view = 'aaa' + (this.messages.length - 1);

				})
				//清空输入框
				this.shurukuang = ""
				console.log("发送到messages");
				console.log(message);
			},
			connectSocketInit() {
				console.log(getApp().globalData.token);
				this.socketTask = uni.connectSocket({
					url: 'ws://localhost:8080/jsb/send-message',
					method: 'GET',
					header: {
						'content-type': 'application/json',
						'token': getApp().globalData.token
					},
					success(e) {
						console.log("websocket连接成功");
						uni.showToast({
							title: '服务器连接成功',
							icon: 'none'
						})
					}
				});
				console.log(this.socketTask);
				// 消息的发送和接收必须在正常连接打开中,才能发送或接收【否则会失败】
				this.socketTask.onOpen((res) => {
					console.log("WebSocket连接正常打开中...！");
					this.is_open_socket = true;
					// 注：只有连接正常打开中 ，才能正常成功发送消息
					this.socketTask.send({
						data: getApp().globalData.token,
						async success() {
							console.log("客户端WS连通的第一个ping发送成功");
						},
					});
					// 注：只有连接正常打开中 ，才能正常收到消息
					this.socketTask.onMessage((res) => {
						let newList = {
							username: '系统',
							is_user: false,
							message: res.data
						}
						this.messages.push(newList)
						console.log("收到服务器内容：" + res.data);
						//定时器方法
						// uniapp需要设置定时器才能实现自动滑动到最底层的效果，如果是开发小程序则不需要使用定时器
						this.$nextTick(function() {
							this.scroll_into_view = 'aaa' + (this.messages.length - 1);

						})
						console.log(this.scroll_into_view)
					});
				})
				// 这里仅是事件监听【如果socket关闭了会执行】
				this.socketTask.onClose(() => {
					console.log("已经被关闭了")
					console.log("尝试重新连接");
					this.is_open_socket = false;
					this.connectSocketInit()
				})

			}

		}
	}
</script>

<style>
	/* 设置长度 */
	scroll-view {
		height: 1213.76rpx;
	}

	.user_message {
		background: #327dfd;
		font-size: 19px;
		font-weight: Normal;
		text-align: center;
		color: undefined;
		line-height: 25px;
		letter-spacing: 1px;
	}

	.user_message {
		word-wrap: break-all;
		margin-top: 54.34rpx;
		border-radius: 36.23rpx 36.23rpx 0rpx 36.23rpx;
		padding: 27.17rpx;
		margin-right: 27.17rpx;
		margin-left: auto;
		color: #FFFFFF;
	}

	.system_message {
		;
		background: #f4f5f5;
		font-size: 19px;
		text-align: left;
		color: #333333;
		line-height: 25px;
		letter-spacing: 1px;
	}

	.system_message {
		word-wrap: break-all;
		margin-top: 54.34rpx;
		border-radius: 36.23rpx 36.23rpx 36.23rpx 0rpx;
		padding: 27.17rpx;
		margin-left: 27.17rpx;
	}

	.fasongkuang {
		width: 414px;
		height: 64px;
		opacity: 1;
		background: #ffffff;
	}

	.fasongkuang {
		display: flex;
		position: fixed;
		bottom: 0px;
	}

	.shurukuang {
		width: 274px;
		height: 48px;
		opacity: 1;
		background: #f2f2f2;
		font-size: 18px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: left;
		color: #333333;
		line-height: 24px;
	}

	.shurukuang {
		margin-top: 14.49rpx;
		margin-left: 16px;
		border-radius: 10px 10px 10px 10px;
		padding-left: 36.23rpx;
		padding-right: 36.23rpx;
	}

	.send_button {
		width: 60px;
		height: 48px;
		opacity: 1;
		background: #327dfd;
		font-size: 16px;
		font-family: Microsoft YaHei, Microsoft YaHei-Normal;
		font-weight: Normal;
		text-align: center;
		color: #ffffff;
		line-height: 22px;
	}

	.send_button {
		margin-top: 14.49rpx;
		display: flex;
		flex-direction: column;
		border-radius: 21.73rpx 21.73rpx 21.73rpx 21.73rpx;
	}

	.send_button_text {
		margin: auto;
	}
</style>
