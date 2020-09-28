<template>
  <view class="timer">
    <image src="../../static/moves/侧平举.gif" />
    <picker mode="multiSelector" :range="timeList" :value="timeIndex" @change="bindTimeChange">
      <view>{{groups}}组 {{times}}次 休息{{rest}}秒</view>
    </picker>
    <button @click="countDown" type="primary" :disabled="!toStart">开始</button>
    <button @click="stop" type="primary" id="stop" :disabled="!isStop">暂停</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      timeList: [
        ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10"],
        ["2", "4", "6", "8", "12", "14", "16", "18", "20"],
        ["10", "20", "30", "40", "50", "60"],
      ],
      timeIndex: [0, 0, 0],
      groups: "0",
      times: "0",
      rest: "0",
      move: "哑铃侧平举",
      username: "",
      totalTime: 0,
      counter: 0,
      isStop: false,
      toStart: true,
    };
  },
  computed: {},
  onLoad() {
    this.username=uni.getStorageSync("userinfo").nickName
    if (uni.getStorageSync("timer3")) {
      this.groups = uni.getStorageSync("timer3")[0];
      this.times = uni.getStorageSync("timer3")[1];
      this.rest = uni.getStorageSync("timer3")[2];
    }
    if (uni.getStorageSync("totalTime")) {
      this.totalTime = uni.getStorageSync("totalTime");
    }
  },
  methods: {
    bindTimeChange(e) {
      console.log("picker发送选择改变，携带值为", e.target.value);
      this.timeIndex = e.target.value;
      this.groups = this.timeList[0][this.timeIndex[0]];
      this.times = this.timeList[1][this.timeIndex[1]];
      this.rest = this.timeList[2][this.timeIndex[2]];
      uni.setStorageSync("timer3", [this.groups, this.times, this.rest]);
    },
    countDown() {
      this.times--;
      if (this.times <= 0 && this.groups > 1) {
        this.isStop = false;
        this.groups--;
        this.times = this.timeList[1][this.timeIndex[1]];
        clearInterval(this.counter);
        var rest = setInterval(() => {
          this.rest--;
          if (this.rest < 10) {
            this.rest = "0" + this.rest;
          }
          if (this.rest <= 0) {
            clearInterval(rest);
            this.rest = this.timeList[2][this.timeIndex[2]];
            this.start();
          }
        }, 1000);
      }
      if (this.times <= 0 && this.groups == 1) {
        clearInterval(this.counter);
        var that = this;
        // 单次运动时间
        var seconds =
          uni.getStorageSync("timer3")[0] * uni.getStorageSync("timer3")[1] * 2;
        var minute = Math.floor(seconds / 60);
        var second = seconds % 60;
        var time = minute + "分" + second + "秒";
        uni.showModal({
          title: "提示",
          content: "完成训练，是否分享动态？",
          success: function (res) {
            if (res.confirm) {
              console.log("用户点击确定");
              uni.navigateTo({
                url: "/pages/edit/index?move=哑铃侧平举&time=" + time,
              });
            } else if (res.cancel) {
              console.log("用户点击取消");
            }

            //总时间以秒为单位
            var total =
              parseInt(that.totalTime) +
              parseInt(uni.getStorageSync("timer3")[0]) *
                parseInt(uni.getStorageSync("timer3")[1]) *
                2;
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
    start() {
      this.isStop = true;
      this.toStart = false;
      this.counter = setInterval(this.countDown, 2000);
    },
    stop() {
      this.isStop = false;
      this.toStart = true;
      clearInterval(this.counter);
    },
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