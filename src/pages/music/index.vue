<template>
    <view class="content">
        <input type="text" v-model="query" placeholder="请输入要搜索的歌曲或歌手名称">
		<button id="search" type="primary" @click="search">搜索</button>
		<button id="showList" @click="show">歌单</button>
		<ul>
            <li v-for="(item,index) in songList" @click="add(item.id,item.name)" :key="index">
				{{item.name}}——{{item.artists[0].name}}  
			</li>
        </ul>
		
		<popup ref="popupRef">
			<view class="box" >
          		<ul>
					<li v-for="(item,index) in playList" @click="play(item.id,item.name)" :key="index">
						{{item.name}}
					</li>	  
				</ul>
        	</view>
		</popup>
		<audio v-bind:src="songurl" controls="controls" :name="songName" :poster="picUrl" :author="artist"></audio>
    </view>
</template>

<script>
	import popup from "../../components/popup-layer"
	export default {
		components:{
			popup
		},
		data() {
			return{
				playList:[],
				query: "",
				songList: [],
				songurl: "",
				songName: "",
				artist:"",
				picUrl:""
			}
		},
		
		methods: {
			search(){
				var that = this
				wx.request({
					url: "https://autumnfish.cn/search?keywords=" + that.query,
					success(res){
						that.songList = res.data.result.songs
						console.log(res)
					}
				})
			},
			add(id,name){
				var song = {}
				song.id = id
				song.name = name
				this.playList.push(song)
			},
			show(){
				this.$refs.popupRef.show()
			},
			play(id,name){
				this.songName = name
				var that = this
				wx.request({
					url:"https://autumnfish.cn/song/detail?ids="+ id,
					success(res){
						that.artist = res.data.songs[0].ar[0].name
						that.picUrl = res.data.songs[0].al.picUrl
					}
				})
				
				wx.request({
					url:"https://autumnfish.cn/song/url?id="+ id,
					success(res){
						that.songurl= res.data.data[0].url
					}
				})
				this.$refs.popupRef.close()
			}
		}
	}
</script>

<style>
#showList{
	display: block;
	width: 60px;
	height: 100px;
	position: fixed;
	right:0px;
	z-index: 999;
}
audio{
	position: fixed;
	bottom: 0;
}
li{
	padding:10px 10px;
	margin:10px 10px;
	background-color:#faefda;
	border-radius: 25px;
}
li:active{
	background-color:#f8d490;
}
ul{
	background-image: url("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595178842474&di=5171654974bbb6bd2067e8ec0e4a1a5f&imgtype=0&src=http%3A%2F%2Fwww.gxyy.net%2Fuploadfile%2F2019%2F0916%2F1568616638804694.jpg");
}
input{
	display: block;
	margin: 5px 15px;
	height: 40px;
}
#search{
	margin:10px 30px;
}
</style>
