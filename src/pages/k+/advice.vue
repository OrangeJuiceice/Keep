<template>
  <div>
    <textarea name id cols="25" rows="20" placeholder="请输入您的意见或建议..." v-model="advices"></textarea>
    <button class="primary" @click="advice" type="primary">提交反馈</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      advices: "",
      name: ""
    };
  },
  onLoad(option) {
    this.name = option.nickName;
  },
  methods: {
    advice() {
      var that = this;
      if (that.advices != "") {
        wx.cloud.init();
        var db = wx.cloud.database();
        db.collection("advice")
          .add({
            data: {
              advice: that.advices,
              nickName: that.name
            }
          })
          .then(function(res) {
            that.advices = "";
            wx.showToast({
              title: "提交成功",
              icon: "success",
              duration: 1500
            });
          });
      } else {
        wx.showToast({
          title: "还没填写反馈哦",
          icon: "none",
          duration: 1500
        });
      }
    }
  }
};
</script>

<style scoped>
textarea {
  margin: 10px;
}
button {
  margin: 10px 30px;
}
</style>