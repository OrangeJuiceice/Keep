<template>
    <view>
        <view class="typing">
            <textarea v-model="comment" placeholder="在这里写下你的感受吧^-^" name="" id="" cols="30" rows="10"></textarea>
        </view>
        <view><text class="line">  </text></view>

        <view><image :src="tempFilePath" alt="" class="normalImage"></image></view>
        <view><text class="line"></text>
        <button @click="submit" type="primary" class="postMoment">发布动态</button>
</view>
        <view><text></text></view>
    </view>
</template>

<script>

    export default {
        data() {
            return {
                tempFilePath:"",
                comment:"",
                tempFileID:"",
                newComment:[],
                username:""

            }
        },
        onLoad(option){
             this.tempFilePath = option.tempFilePath
             this.username=uni.getStorageSync("userinfo").nickName
        },
        methods: {
            
            submit() {
                //console.log("ok")
                var that = this
                var name = this.tempFilePath.split("/")[this.tempFilePath.split("/").length-1]
                wx.cloud.init()
                
                wx.cloud.uploadFile({
                    
                    cloudPath:'images/' + name,
                    filePath: this.tempFilePath
                }).then(function(res){
                    //console.log(res)
                    var db =wx.cloud.database()
                    var createTime = new Date().toLocaleString()//db.serverDate()//
                    db.collection("moments").add({//这个test是在云开发那里新建的
                        data:{
                            imageURL: res.fileID,
                            comment: that.comment,
                            createTime: createTime,
                            likes: 0,
                            newComment: [],
                            username:that.username
                        }}
                    )
                    wx.navigateBack() 
                })
                
                //wx.navigateTo
                
            }
        },
    }
</script>

<style scoped>
button{
  margin-top: 30rpx;
  margin-bottom: 30rpx;
}
.postMoment{
    width: 80%;
}
.typing{
    border:1px solid rgba(241,241,241,.98);
    border-radius:5px;
    background-color:rgba(241,241,241,.98);
    width: 98%;
    height: 100px;
    padding: 10px;
    resize: none;
}
.line{
  float:right;
  width: 100%;
  height: 1px;
  margin-top: -0.5em;
  background:#d4c4c4;
  position: relative;
  text-align: center;
}
.normalImage{
    display: block;
  margin: 0 auto;
  width: 95%;
  margin-top: 3px;
  margin-bottom: 2px;
}
</style>