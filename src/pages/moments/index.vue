<template>
  <view class="middle">
    <button @click="addMoment" type="primary" class="newMoment">发布新动态</button>
    <view v-for="moment in moments" :key="moment" class="singleMoment">
      <text>{{username}}</text>
      <br />
      <text class="moment">{{moment.comment}}</text>
      <image :src="moment.imageURL" class="normalImage" />
      <view></view>
      <view>
        <text class="time">{{moment.createTime}}</text>
        <view class="button-sp-area">
          <image src="../../static/like.png" @click="like(moment)" class="like" />
          {{moment.likes}}
          <image src="../../static/delete.png" @click="remove(moment)" class="delete" />
        </view>
      </view>
      <view class="clear"></view>
      <view>
        <textarea name id placeholder="在这里写下你的评论吧^-^" class="comments" v-model="comments"></textarea>
        <button class="mini-btn" type="primary" size="mini" @click="toComment(moment)">评论</button>
        <!--<button class="mini-btn" type="default" size="mini" @click="changeShow">查看评论</button>-->
      </view>
      <view v-if="show">
        <view v-for="comment in moment.newComment" :key="comment" class="comment">
          <text>{{username}}</text><br>
          <text>{{comment}}</text>
        </view>
      </view>

      <view>
        <text class="line"></text>
      </view>
    </view>

    <view>
      <text></text>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      moments: [],
      comments: "",
      show: true,
      username:""
    };
  },
  onLoad() {
    this.username=uni.getStorageSync("userinfo").nickName
  },
  onShow() {
    //this.moments =
    var that = this;
    wx.cloud.init();
    var db = wx.cloud.database();
    db.collection("moments")
      .orderBy("createTime", "desc")
      .get()
      .then(function (res) {
        //这个test是在云开发那里新建的
        console.log(res);
        that.moments = res.data;
      });
  },
  methods: {
    addMoment() {
      wx.chooseImage({
        //发朋友圈
        count: 9,
        sizeType: ["original", "compressed"],
        sourceType: ["album", "camera"],
        success(res) {
          wx.navigateTo({
            url: "/pages/postMoment/index?tempFilePath=" + res.tempFilePaths[0],
          });
          //console.log(res)
          //const tempFilePaths = res.tempFilePaths
        },
      });
    },
    like(moment) {
      //点赞功能
      //console.log(moment)
      wx.cloud.init();
      var db = wx.cloud.database();
      var newLikes = moment.likes + 1;
      db.collection("moments")
        .doc(moment._id)
        .update({
          data: {
            //likes: this.likes.inc(1)
            likes: newLikes,
            
          },
        })
        .then(
          wx.showToast({
            title: "点赞成功",
          })
        )
        .catch(console.error);
        wx.navigateTo({//页面刷新
          url:"/pages/blank/blank"
        })
    },
    toComment(moment) {
      //加评论的时候把用户信息加上
      wx.cloud.init();
      var db = wx.cloud.database();
      var remark = moment.newComment;
      remark.push(this.comments);
      db.collection("moments")
        .where({
          _id: moment._id,
        })
        .update({
          data: {
            newComment: remark,
             username:uni.getStorageSync("userinfo").nickName
          },
        })
        .then(
          wx.showToast({
            title: "评论成功",
          })
        );
      this.comments = "";
    },

    remove(moment) {
      //删除
      wx.cloud.init();
      var db = wx.cloud.database();
      db.collection("moments")
        .where({
          _id: moment._id,
        })
        .remove()
        .then(console.log("Delete successfully"))
        .catch(console.log("Damnmnmnmn"));
      wx.navigateTo({//页面刷新
          url:"/pages/blank/blank"
        })
    },
    changeShow() {
      this.show = !this.show;
      
    },
  },
};
</script>

<style scoped>
.page {
  background-color: rgba(241, 241, 241, 0.98);
}
button {
  margin-top: 30rpx;
  margin-bottom: 30rpx;
}
.newMoment {
  width: 80%;
}
.moment{
    font-size:1.2em;
}
.time{
    color:gray;
    float:left;
}
.singleMoment {
  border: 0px rgb(175, 175, 175) solid;
}
.line {
  float: right;
  width: 100%;
  height: 1px;
  margin-top: -0.2em;
  background: #d4c4c4;
  position: relative;
  text-align: center;
}
.middle {
  margin: 0 auto;
  width: 95%;
  height: 60px;
  background-color: white;
  margin-top: 2%;
}
.normalImage {
  display: block;
  margin: 0 auto;
  width: 95%;
  margin-top: 3px;
  margin-bottom: 2px;
}
.comments {
  width: 80%;
  height: 1.5em;
  background-color: #EFEFEF;
  border-radius: 5px;
  display: inline-block;
}
.like,
.delete {
  width: 25px;
  height: 25px;
}
.mini-btn {
  display: inline-block;
  margin: 0 .3em;
  padding: .3em .5em;
  line-height: 1.5em;
}
.comment{
  background-color: #FAFAFA;
  margin: 1em;
}
.button-sp-area{
  width: 60px;
  margin: 0.5em 0;
  float: right;
}
.clear{
  clear: both;
}
</style>