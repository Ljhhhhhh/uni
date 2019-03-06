<template>
  <view>
    <!-- #ifdef MP-WEIXIN -->
    <button
      class="login_btn"
      @getuserinfo="getUserinfo"
      open-type="getUserInfo"
      @tap="beforeGetUserinfo"
    >
      点击登录
    </button>
    <!-- #endif -->
  </view>
</template>

<script>
  let _self;
export default {
  onLoad(options) {
    _self = this;
    // #ifdef MP
    uni.login({
      success: function(loginRes) {
        uni.request({
        	url: _self.apiServer +'member&m=codeToSession&code=' + loginRes.code,
        	method: 'GET',
        	data: {},
        	success: res => {
            _self.openid = res.data.openid;
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
    return {
      openid: ''
    };
  },
  methods: {
    autoGetUserInfo(e) {
      if (e) {
        console.log('用户信息：', e);
      }
      uni.getUserInfo({
        success: (info) => {
          const {nickName, avatarUrl} = JSON.parse(info.rawData)
          uni.request({
          	url: _self.apiServer +'member&m=login',
            header: {'content-type' : "application/x-www-form-urlencoded"},
          	method: 'POST',
          	data: {
              openid : _self.openid,
              name   : nickName,
              face   : avatarUrl,
            },
            success:(res) => {
            	console.log(res);
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
          console.log(e);
        }
      });
    },
    beforeGetUserinfo() {
      wx.getUserInfo({
        withCredentials: true,
        success: function(res) {
          console.log(res, 'userinfores');
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
