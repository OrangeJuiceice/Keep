<template>
  <view class="timer">
    <image src="../../static/moves/plank.jpg" />
    <picker mode="multiSelector" :range="timeList" :value="timeIndex" @change="bindTimeChange">
      <view>{{minutes}}:{{seconds}}</view>
    </picker>
    <button @click="start" type="primary" :disabled = "isStop">开始</button>
    <button @click="stop" type="primary" id="stop" :disabled="!isStop">暂停</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      timeList: [
        ["00", "01", "02", "03", "04", "05", "06", "07", "08", "09", "10"],
        ["00", "10", "20", "30", "40", "50"],
      ],
      timeIndex: [0, 0],
      minutes: "00",
      seconds: "00",
      move: "平板支撑",
      username: "",
      totalTime: 0,
      counter:0,
      isStop:false
    };
  },
  computed: {},
  onLoad() {
    this.username=uni.getStorageSync("userinfo").nickName
    if (uni.getStorageSync("timer0")) {
      this.minutes = uni.getStorageSync("timer0")[0];
      this.seconds = uni.getStorageSync("timer0")[1];
    }
    if (uni.getStorageSync("totalTime")) {
      this.totalTime = uni.getStorageSync("totalTime");
    }
  },
  methods: {
    bindTimeChange(e) {
      console.log("picker发送选择改变，携带值为", e.target.value);
      this.timeIndex = e.target.value;
      this.minutes = this.timeList[0][this.timeIndex[0]];
      this.seconds = this.timeList[1][this.timeIndex[1]];
      uni.setStorageSync("timer0", [this.minutes, this.seconds]);
    },
    countDown() {
      
        this.seconds--;

        if (this.seconds <= 0 && this.minutes > 0) {
          this.minutes--;
          if (this.minutes < 10) {
            this.minutes = "0" + this.minutes;
          }
          this.seconds = 59;
        }

        if (this.seconds < 10) {
          this.seconds = "0" + this.seconds;
        }

        if (this.seconds <= 0 && this.minutes <= 0) {
          clearInterval(this.counter);
          var that = this;
          //单次运动时间
          var minute = uni.getStorageSync("timer0")[0];
          var second = uni.getStorageSync("timer0")[1];
          var time = minute + "分" + second + "秒";

          uni.showModal({
            title: "提示",
            content: "完成训练，是否分享动态？",
            success: function (res) {
              if (res.confirm) {
                console.log("用户点击确定");
                uni.navigateTo({
                  url: "/pages/edit/index?move=平板支撑&time=" + time,
                });
              } else if (res.cancel) {
                console.log("用户点击取消");
              }

              //总时间以秒为单位
              var total =
                parseInt(that.totalTime) +
                parseInt(uni.getStorageSync("timer0")[0]) * 60 +
                parseInt(uni.getStorageSync("timer0")[1]);
              uni.setStorageSync("totalTime", total);

              wx.cloud.init();
              var db = wx.cloud.database();
              db.collection("Keep").add({
                data: {
                  datetime: new Date().toLocaleString(),
                  move: that.move,
                  time: time,
                  username: that.username,
                  totaltime: total,
                  likes: 0,
                  newComment: [],
                  
                },
              });
            },
          });
        }
      
    },
    start(){
      this.isStop = true
      this.counter = setInterval(this.countDown,1000)
    },
    stop(){
      this.isStop = false
      clearInterval(this.counter)
    }
  },
};
</script>

<style lang="css" scoped>
.timer {
  text-align: center;
  font-size: 2em;
  margin-top: 0.5em;
}
button {
  margin: 1em;
  border-radius: 0.5em;
}
</style>