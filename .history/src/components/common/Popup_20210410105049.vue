<template>
  <div class="popup" v-show="popupVisible">
    <div class="popupbg" @click="hide"></div>
    <div class="popup-wrapper">
      <div class="popup-title" v-if="title && title.length > 0">{{title}}</div>
      <div class="popup-btn"
        :class="{'danger': item.type==='danger'}"
        v-for="(item, index) in btn"
        :key="index"
        @click="item.click">{{item.text}}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'popup',
  props: {
    title: String,
    btn: Array
  },
  data() {
    return {
      popupVisible: false
    }
  },
  methods: {
    show() {
      console.log('niaho1')
      this.popupVisible = true
    },
    hide() {
      console.log('niaho')
      this.popupVisible = false
    }
  }
}
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global";
 .popup {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 2000;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, .4);
    .popup-bg {
      width: 100%;
      height: 100%;
      z-index: 2000;
    }
    .popup-wrapper {
      position: fixed;
      left: 0;
      bottom: 0;
      z-index: 2000;
      width: 100%;
      background: white;
      .popup-title {
        width: 100%;
        height: px2rem(44);
        border-bottom: px2rem(1) solid #eee;
        font-size: px2rem(12);
        line-height: px2rem(14);
        padding: px2rem(15);
        box-sizing: border-box;
        color: #999;
        @include center;
      }
      .popup-btn {
        &.danger {
        }
      }
    }
  }
</style>
