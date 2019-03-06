<template>
	<view>
		<!-- #ifdef MP-WEIXIN -->
		<button type="primary" @getuserinfo="getUserinfo" open-type="getUserInfo">
			点击登录
		</button>
		<!-- #endif -->
	</view>
</template>

<script>
	let _self, openid, session_key, pageOptions;
	export default {
		onLoad(options) {
			_self = this;
			pageOptions = options;
			// #ifdef MP-WEIXIN
			uni.login({
				success: function(loginRes) {
					uni.request({
						url: _self.apiServer + 'member&m=codeToSession&code=' + loginRes.code,
						method: 'GET',
						data: {},
						success: res => {
							openid = res.data.openid;
							session_key = res.data.session_key;
							_self.autoGetUserInfo();
						},
						fail: () => {},
						complete: () => {}
					});
				}
			});
			// #endif
		},
		data() {
			return {};
		},
		methods: {
			autoGetUserInfo(e) {
				if (e) {
					console.log('用户信息：', e);
				}
				uni.getUserInfo({
					success: (info) => {
						const {
							nickName,
							avatarUrl
						} = JSON.parse(info.rawData)
						uni.request({
							url: _self.apiServer + 'member&m=login',
							header: {
								'content-type': "application/x-www-form-urlencoded"
							},
							method: 'POST',
							data: {
								openid: openid,
								name: nickName,
								face: avatarUrl,
							},
							success: (res) => {
								console.log('userdata:', res);
								res = res.data;
								if (res.status == 'ok') {
									uni.showToast({
										title: "登录成功"
									});
									uni.setStorageSync('SUID', res.data.u_id + '');
									uni.setStorageSync('SRAND', res.data.u_random + '');
									uni.setStorageSync('SNAME', res.data.u_name + '');
									uni.setStorageSync('SFACE', res.data.u_face + '');
									// 跳转
									if (pageOptions.backtype == 1) {
										uni.redirectTo({
											url: pageOptions.backpage
										});
									} else {
										uni.switchTab({
											url: pageOptions.backpage
										});
									}
								} else {
									uni.showToast({
										title: res.data
									});
								}
							},
							fail: () => {},
							complete: () => {}
						});
					},
					fail(e) {
						// #ifdef MP-WEIXIN
						uni.showToast({
							title: '请点击按钮登录',
							icon: 'none',
							duration: 2000
						})
						// #endif
						// #ifdef APP-PLUS
						uni.login({
							success: (res) => {
								uni.getUserInfo({
									success: (info) => {
										uni.request({
											url: _self.apiServer + 'member&m=login',
											method: 'POST',
											header: {
												'content-type': "application/x-www-form-urlencoded"
											},
											data: {
												openid: info.userInfo.openId,
												name: info.userInfo.nickName,
												face: info.userInfo.avatarUrl,
											},
											success: res => {
												res = res.data;
												// 登录成功 记录会员信息到本地
												if (res.status == 'ok') {
													uni.showToast({
														title: "登录成功"
													});
													uni.setStorageSync('SUID', res.data.u_id + '');
													uni.setStorageSync('SRAND', res.data.u_random + '');
													uni.setStorageSync('SNAME', res.data.u_name + '');
													uni.setStorageSync('SFACE', res.data.u_face + '');
													// 跳转
													if (options.backtype == 1) {
														uni.redirectTo({
															url: options.backpage
														});
													} else {
														uni.switchTab({
															url: options.backpage
														});
													}
												} else {
													uni.showToast({
														title: res.data
													});
												}
											},
											fail: (e) => {
												console.log(JSON.stringify(e));
											}
										});
									},
									fail: () => {
										uni.showToast({
											title: "微信登录授权失败"
										});
									}
								})
							},
							fail: () => {
								uni.showToast({
									title: "微信登录授权失败"
								});
								uni.hideLoading();
							}
						})
						// #endif
						console.log(e);
					}
				});
			},
			getUserinfo: function(e) {
				this.autoGetUserInfo(e)
			},
		}
	};
</script>

<style></style>
