<template>
  <div>
    <textarea name id cols="30" rows="5" v-model="comment" placeholder="Your Thoughts"></textarea>
    <image :src="tempFilePath" />
    <view>完成{{move}} {{time}}</view>
    <button @click="addImage" type="primary">添加图片</button>
    <button @click="publish" type="primary">发布动态</button>
  </div>
</template>

<script>
    export default {
        data() {
            return {
                comment:"",
                tempFilePath: "",
                time:"",
                move:"",
                username:""
            }
        },
        onLoad(option){
            console.log(option)
            this.username=uni.getStorageSync("userinfo").nickName
            this.move = option.move;
            this.time = option.time
        },
        methods: {
            addImage(){
                var that = this
                wx.chooseImage({
                    count:1,
                    success:function(res){
                        that.tempFilePath = res.tempFilePaths[0]
                    }
                })
            },
            publish() {
                var splitPaths = this.tempFilePath.split("/")
                var that = this
                wx.cloud.init();
                wx.cloud.uploadFile({
                    cloudPath:"images/"+ splitPaths[splitPaths.length-1],
                    filePath:this.tempFilePath
                })
                .then(function (res) {
                    var db = wx.cloud.database();
                    db.collection("moments").add({
                        data:{
                            imageURL:res.fileID,
                            comment: that.comment,
                            datetime:new Date().toLocaleString(),
                            move:that.move,
                            time:that.time,
                            createTime: new Date().toLocaleString(),
                            likes: 0,
                            newComment: [],
                            username:that.username
                        }
                    })
                })
                wx.navigateBack();
            }
        },
    }
</script>

<style scoped>
button{
  margin: 0.5em 1em;
  border-radius: 1em;
}
</style>