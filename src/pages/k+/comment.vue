<template>
  <div>
    <p class="msg">{{msg}}</p>

    <div class="container">
      <div class="row" v-for="item in comments" :key="item">
        <label class="comment">{{item.username}}</label>
        <br />
        <div class="relatedMoment">
          <label>{{item.comment}}</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      comments: [],
      msg: ""
    };
  },
   onLoad(option){
            this.name=option.nickName
           
            var that=this
			wx.cloud.init()
			var db=wx.cloud.database()
			db.collection('moments').where({username:that.name}).get().then(function(res){
                console.log(res.data)
                that.comments=res.data

                if (that.comments==0){
                that.msg='暂无评论，快去评论吧！'}
            })
             
            
            
        },
  onShow() {}
};
</script>

<style scoped>
/* .msg {
  text-align: center;
}
.relatedMoment {
  text-align: left;
  border: 1px solid gray;
  margin-left: 5px;
  width: 100%;
  height: 30px;
}
.row {
  margin: 5px;
} */
.row{
    border: 1px solid gray;
    margin:10px 5px 10px 5px;
    
    
}
label{
    font-size: 1.2em;
}
</style>