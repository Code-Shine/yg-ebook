<template>
  <transition name="popup-slide-up">
    <div class="ebook-popup-list" v-show="fontFamilyVisible">
      <!-- 弹出框的标题部分 -->
      <div class="ebook-popup-title">
        <!-- 隐藏弹出框的图标 -->
        <div class="ebook-popup-title-icon" @click="hide">
          <span class="icon-down2"></span>
        </div>
        <span class="ebook-popup-title-text">选择字体</span>
      </div>
      <!-- 弹出框的字体样式列表 -->
      <div class="ebook-popup-list-wrapper">
        <div class="ebook-popup-item" v-for="(item, index) in
          fontFamilyList" :key="index">
          <div class="ebook-popup-item-text"
           :class="{'selected': isSelected(item)}">{{item.font}}</div>
           <div class="ebook-popup-item-check">
             <span class="icon-check"></span>
           </div>
        </div>

      </div>
    </div>
  </transition>
</template>

<script>
import { FONT_FAMILY } from '../../utils/book'
import { ebookMixin } from '../../utils/mixin'
export default {
  mixins: [ebookMixin],
  data() {
    return {
      fontFamilyList: FONT_FAMILY
    }
  },
  methods: {
    isSelected(item) {

    },
    hide() {
      this.setFontFamilyVisible(false)
    }
  }
}
</script>

<style lang='scss' rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global.scss";
  .ebook-popup-list{
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 300;
    width: 100%;
    box-shadow: 0 px2rem(-4) px2rem(6) rgba(0,0,0,.1);
    background: white;
    .ebook-popup-title{
      position: relative;
      padding: px2rem(15);
      box-sizing: border-box;
      border-bottom: px2rem(1) solid #b8b9bb;
      text-align: center;
      @include center;
      .ebook-popup-title-icon{
        position: absolute;
        left: px2rem(15);
        top: 0;
        height: 100%;
        font-size: px2rem(16);
        font-weight: bold;
        @include center;
      }
      .ebook-popup-title-text{
        font-size: px2rem(14);
        font-weight: bold;
      }
    }
    .ebook-popup-list-wrapper{
      .ebook-popup-item {
        display: flex;
        .ebook-popup-item-text {
          flex:1;
          font-size: px2rem(14);
          text-align: left;
        }
        .ebook-popup-item-check {
          flex:1;
          text-align: right;
        }
      }
    }
  }

</style>
