<template>
  <div>
    <div class="top">
      <div class="userinfo">
        <view>
          <open-data type="userAvatarUrl" class="profile"></open-data>
        </view>
        <open-data type="userNickName" class="name"></open-data>
        <img :src="image" class="gender" />
      </div>
    </div>

    <div class="container">
      <div class="row-for-time">
        <div class="training-time">
          <label class="time">运动总时长</label>
          <br />
          <label class="all-time">
            <view>
              <text>{{t_t}}</text>
            </view>
          </label>
        </div>
      </div>

      <div class="row" @click="comment">
        <label class="left">
          <img src="/static/comment.png" class="icon"/> 我的动态
        </label>
        <label class="right">></label>
      </div>

      <div class="row" @click="advice">
        <label class="left">
          <img src="/static/advice.png" class="icon"/>意见反馈
        </label>
        <label class="right">></label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 用户信息
      userinfo: {},
      canIUse: wx.canIUse("button.open-type.getUserInfo"),
      image: "",
      total_time: [],
      t_t: "0",
      min: 0,
      sec: 0
    };
  },

  onShow() {
    var that = this;
    wx.cloud.init();
    var db = wx.cloud.database();
    db.collection("Keep")
      .orderBy("_id", "desc")
      .get()
      .then(function(res) {
        that.total_time = res.data;
        console.log(that.total_time);

        for (var i = 0; i < that.total_time.length; i++) {
          if (that.total_time[i].time) {
            var min = that.total_time[i].time.split("分")[0];
            var sec = that.total_time[i].time.split("分")[1].split("秒")[0];
            sec = parseInt(sec);
            that.sec = that.sec + sec;
            if (that.sec > 59) {
              that.min += 1;
              that.sec = that.sec - 60;
            }
            min = parseInt(min);
            that.min = that.min + min;
            console.log(that.min, that.sec);
          }
        }
        if (that.sec < 10) {
          that.sec = "0" + that.sec;
        }
        if (that.min < 10) {
          that.min = "0" + that.min;
        }
        that.t_t = that.min + "分" + that.sec + "秒";
        that.min=0;
        that.sec=0
      });
    wx.getSetting({
      success(res) {
        if (res.authSetting["scope.userInfo"]) {
          // 已经授权，可以直接调用 getUserInfo 获取头像昵称
          wx.getUserInfo({
            success: function(res) {
              console.log(res.userInfo);
              that.userinfo = res.userInfo;
              if (that.userinfo.gender == 1) {
                that.image = "/static/male.png";
              } else {
                that.image = "/static/female.png";
              }
            }
          });
        }
      }
    });
  },
  methods: {
    bindGetUserInfo(e) {
      console.log(e.detail.userInfo);
    },
    advice() {
      uni.navigateTo({
        url: "/pages/k+/advice?nickName=" + this.userinfo["nickName"]
      });
    },
    comment() {
      uni.navigateTo({
        url: "/pages/k+/comment?nickName=" + this.userinfo["nickName"]
      });
    }
  }
};
</script>

<style scoped>

.container {
  margin-top: 10px;
  background: #ffffff;
  font-size: 15px;
}
.row {
  padding: 0px 18px;
  border-bottom: 1px #e8e8e8 solid;
  height: 55px;
  line-height: 55px;
}
.row-for-time {
  padding: 0px 18px;
  border-bottom: 1px #e8e8e8 solid;
  height: 80px;
  line-height: 30px;
}
.all-time {
  font-size: 30px;
}
.profile {
  float: left;
  width: 50px;
  height: 50px;
  padding-top: 10px;
  margin-left: 10px;
}
.name {
  float: left;
  color: black;
}
.training-time {
  text-align: center;
}
.right {
  float: right;
  color: #c8c8c8;
  line-height: 55px;
}
.left {
  width: 80%;
  float: left;
}

.top {
  height: 100px;
  width: 100%;
  background: #584F60;
  padding-top: 30px;
  display: block;
}
.userinfo {
  padding-bottom: 1px;
  text-align: center;
  /* margin-left: 40%; */
}
img {
  width: 20px;
  height: 20px;
}
.gender {
  float: left;
  margin-top: 30px;
}

.name {
  padding-top: 30px;
  padding-left: 5px;
  color: #ffffff;
  font-size: 16px;
  float: left;
}
.underline {
  border: 1px solid #ffffff;
  border-radius: 5px;
  text-align: center;
}
.a-line {
  background: #ea5149;
  border: none;
  display: inline;
  font-size: 16px;
  color: #ffffff;
  text-decoration: underline;
}
.icon {
  margin-right: 10px;
  margin-top: 15px;
}
</style>