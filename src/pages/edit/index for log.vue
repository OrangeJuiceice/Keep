<template>
<view>
<view wx:if="canIUse">
  <view class='header'>
    <image src='/static/community.png'></image>
  </view>
  <view class='content'>
    <view>申请获取以下权限</view>
    <text>获得你的公开信息(昵称，头像等)</text>
  </view>
  <button class='bottom' type='primary' open-type="getUserInfo" lang="zh_CN" @getuserinfo="bindGetUserInfo">
    授权登录
  </button>

</view>
<view wx:else>请升级微信版本</view>
</view>
</template>

<script>


export default {
   
    data() {
        return {
           canIUse: wx.canIUse('button.open-type.getUserInfo')
        };
    },

     onLoad(option){
    var that = this;
     wx.getSetting({
    success(res) {
          if (res.authSetting['scope.userInfo']) {
                 wx.getUserInfo({
            success: function (res) {
              uni.setStorageSync("userinfo",res.userInfo)
              // 用户已经授权过,调用微信的 wx.login 接口，从而获取code,再直接跳转到主页
              wx.login({
                success: res => {
                  // 获取到用户的 code 之后：res.code
                  console.log("用户的code:" + res.code);
                }
              });
              wx.switchTab({
                url: '/pages/index/index',    //这里填入要跳转目的页面的url
                success: (result) => {
                  console.log("跳转到首页");
                },
                fail: () => {}
              });
            }
          });
        } else {
          // 用户没有授权，显示授权页面,这里不进行操作
        }
      }
    });
  },
 
  onShow () {
    //  wx.getSetting({
    //   success (res){
    //     if (res.authSetting['scope.userInfo']) {
    //       // 已经授权，可以直接调用 getUserInfo 获取头像昵称
    //       wx.getUserInfo({
    //         success: function(res) {
    //           console.log(res.userInfo)
    //         }
    //       })
    //     }
    //   }
    // })
  },
  methods: {
 bindGetUserInfo (res) {
   uni.clearStorageSync()
    if (res.detail.userInfo) {
      //用户按了允许授权按钮
      var that = this;
      // 获取到用户的信息了，打印到控制台上看下
      console.log("用户的信息如下：");
      console.log(res.detail.userInfo);
      uni.setStorageSync("userinfo",res.detail.userInfo)
      //授权成功后,跳转页面
      wx.switchTab({
        url: '/pages/index/index',    //这里填入要跳转目的页面的url
        success: (result) => {
          console.log("跳转到首页");
        },
        fail: () => {}
      });
    } else {
      //用户按了拒绝按钮
      wx.showModal({
        title: '警告',
        content: '您拒绝了授权，将无法进入小程序，请授权之后再进入!',
        showCancel: false,
        confirmText: '返回',
        success: function (res) {
          // 用户没有授权成功，不需要改变 isHide 的值
          if (res.confirm) {
            console.log('用户点击了“返回”');
          }
        }
      });
    }
  }

  }

}


   
 
</script>


<style>
.header {
  margin: 90rpx 0 90rpx 50rpx;
  border-bottom: 1px solid #ccc;
  text-align: center;
  width: 650rpx;
  height: 300rpx;
}

.header image {
  width: 200rpx;
  height: 200rpx;
}

.content {
  margin-left: 50rpx;
  margin-bottom: 90rpx;
}

.content text {
  display: block;
  color: #9d9d9d;
  margin-top: 40rpx;
}

.bottom {
  border-radius: 80rpx;
  margin: 70rpx 50rpx;
  font-size: 35rpx;
}


</style>
