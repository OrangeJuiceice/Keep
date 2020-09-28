<template>
  <view>
    <view class="mainbody">
      <view id="slogan">{{state}}</view>
      <view class="timer">
        <view class="runTime">{{minutes}}:{{seconds}}</view>
      </view>

      <view class="buC">
        <button class="mini-btn" type="primary" size="mini" @click="stop">暂停</button>
        <button class="mini-btn" type="primary" size="mini" @click="start">开始</button>
        <button class="mini-btn" type="primary" size="mini" @click="end">结束</button>
      </view>
    </view>
    <img id="play" @click="play" :src="iconUrl" />
    <img id="next" @click="next" src="/../static/play.jpg" />
  </view>
</template>

<script>
var innerAudioContext = uni.createInnerAudioContext();
export default {
  data() {
    return {
      iconUrl: "/../static/pause.jpg",
      minutes: "00",
      seconds: "00",
      time: 0,
      timeOut: 0,
      stops: false,
      state: "请开始运动",
       username:"",
       move:"跑步"
    };
  },
  onLoad() {
    this.username=uni.getStorageSync("userinfo").nickName
    wx.request({
      url: "https://api.uomg.com/api/rand.music?sort=热歌榜&format=json",
      success(res) {
        innerAudioContext.src = res.data.data.url;
      },
    });
  },
  methods: {
    countDown() {
      this.seconds++;
      if (this.seconds > 59) {
        this.minutes++;
        if (this.minutes < 10) {
          this.minutes = "0" + this.minutes;
        }
        this.seconds = 0;
      }
      if (this.seconds < 10) {
        this.seconds = "0" + this.seconds;
      }
    },
    stop() {
      this.state = "运动已暂停";
      clearInterval(this.time);
    },
    start() {
      clearInterval(this.time)
      this.time = setInterval(this.countDown, 1000);
      this.state = "运动中";
    },
   end(){
        clearInterval(this.time)
        var that = this
        that.timeout = that.minutes + "分" + that.seconds + "秒"
        console.log(that.timeout)
        
         wx.cloud.init();
              var db = wx.cloud.database();
              db.collection("Keep").add({
                data: {
                  datetime: new Date().toLocaleString(),
                  move: "跑步",
                  time: that.timeout,
                  username: that.username,
                  likes: 0,
                  newComment: [],
                  username:that.username
                }
              })
        uni.showModal({
          title: "分享",
          content: "是否分享这次跑步的结果",
          success: function(res) {
            if (res.confirm) {
              console.log("用户点击确定");
              uni.navigateTo({
                  url: "/pages/edit/index?move=跑步&time=" + that.timeout
                });
            } else if (res.cancel) {
              console.log("用户点击取消");
            }
          }
        })
      
     },
    play() {
      if (innerAudioContext.paused == true) {
        innerAudioContext.play();
        this.iconUrl = "/../static/playing.jpg";
      } else {
        innerAudioContext.pause();
        this.iconUrl = "/../static/pause.jpg";
      }
    },
    next() {
      this.iconUrl = "/../static/playing.jpg";
      wx.request({
        url: "https://api.uomg.com/api/rand.music?sort=热歌榜&format=json",
        success(res) {
          innerAudioContext.src = res.data.data.url;
          innerAudioContext.play();
        },
      });
    },
  },
};
</script>

<style>
page {
  height: 100%;
  background-color: #584F60;
}
.timer {
  text-align: center;
  font-size: 2em;
  margin-top: 0.5em;
}
.btn {
  margin-left: 15rem;
  border-radius: 30rem;
  height: 3rem;
  width: 3rem;
  background-color: #093387;
  text-align: center;
}
.btn-yulan {
  font-size: 50%;
  height: 1rem;
  width: 2rem;
  margin-top: 0.9rem;
  margin-left: -1rem;
  color: #c0c0c0;
  position: absolute;
}

#play {
  width: 40px;
  height: 40px;
  position: absolute;
  top: 320px;
  right: 210px;
}
#next {
  width: 40px;
  height: 40px;
  position: absolute;
  top: 320px;
  right: 90px;
}
#logo {
  width: 100%;
  height: 550px;
  position: absolute;
  z-index: -1;
  opacity: 1;
}

#slogan {
  font-size: 2rem;
  font-weight: bold;
  color: white;
  text-align: center;
}
.mainbody {
  width: 100%;
  position: relative;
  top: 100px;
}

.runTime {
  font-size: 4rem;
  color: white;
}
.buC {
  height: 100%;
  margin-top: 1em;
  display: grid;
  grid-template-columns: 33% 34% 33%;
}

</style>