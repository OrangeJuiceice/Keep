<template>
  <view class="list">
    <view
      class="item"
      v-for="(move,index) in moves"
      :key="index"
      @click="goToTimer(index)"
    >{{move}}</view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      moves: [
        '平板支撑','肱二头弯举','哑铃肩部推举','哑铃侧平举','俯身划船(待开发）','颈后深蹲（待开发）','颈前深蹲（待开发）','弓箭步（待开发）'
      ]
    };
  },
  methods: {
    goToTimer(index) {
      uni.navigateTo({ url: "/pages/timer/timer" + index });
    }
  }
};
</script>

<style>
.list {
  margin-top: 1em;
}
.item {
  background-color: #584F60;
  color: white;
  font-size: 1.2em;
  border: 5px solid;
  padding: 0.5em 1em 0.5em 0.3em;
}
</style>
