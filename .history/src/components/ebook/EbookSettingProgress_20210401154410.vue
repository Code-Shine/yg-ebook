<template>
  <transition name="slide-up">
    <div class="setting-wrapper" v-show="menuVisible && settingVisible == 2">
      <!-- 进度条 -->
      <div class="setting-progress" >
        <!-- 阅读时间栏 -->
      <div class="read-time-wrapper">
        <span class="read-time-text">111</span>
        <span class="icon-forward"></span>
      </div>
        <div class="progress-wrapper">
          <!-- 上一章按钮 -->
          <div class="progress-icon-wrapper">
            <span class="icon-back" @click="prevSection()"></span>
          </div>
          <input class="progress" type="range"
                 max="100"
                 min="0"
                 step="1"
                 @change="onProgressChange($event.target.value)" @input="onProgressInput($event.target.value)"
                 :value="progress"
                 :disabled="!bookAvailable"
                 ref="progress">
           <!-- 下一章按钮 -->
          <div class="progress-icon-wrapper">
            <span class="icon-forward" @click="nextSection()"></span>
          </div>
        </div>
        <div class="text-wrapper">
          <span>{{bookAvailable ? progress + '%' : '加载中...'}}</span>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import { ebookMixin } from '../../utils/mixin'
export default {
  mixins: [ebookMixin],
  methods: {
    onProgressChange(progress) { // 拖动松手的调用方法
      this.setProgress(progress).then(() => { // 设置vuex的值然后进行异步的调用displayProgress()
        this.displayProgress()
      })
    },
    onProgressInput(progress) {}, // 拖动过程中调用的方法
    displayProgress() {
      const cfi = this.currentBook.locations.cfiFromPercentage(this.progress / 100)
      console.log(cfi)
    },
    prevSetion() {},
    nextSection() {}
  }
}
</script>

<style lang='scss' rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global.scss";
   .setting-wrapper {
    position: absolute;
    bottom: px2rem(48);
    left: 0;
    z-index: 101;
    width: 100%;
    height: px2rem(90);
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
    .setting-progress {
        position: relative;
        width: 100%;
        height: 100%;
        .read-time-wrapper {
          position: absolute;
          left: 0;
          top: 0;
          width: 100%;
          height: px2rem(40);
          font-size: px2rem(12);
          @include center;
        }
        .progress-wrapper {
          width: 100%;
          height: 100%;
          @include center;
          padding: 0 px2rem(15);
          box-sizing: border-box;
          .progress-icon-wrapper {
            font-size: px2rem(20);
          }
          .progress {
            width: 100%;
            -webkit-appearance: none;
            height: px2rem(2);
            background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
            background-size: 0 100%!important;
            margin: 0 px2rem(10);
            &:focus {
              outline: none;
            }
            &::-webkit-slider-thumb {
              -webkit-appearance: none;
              height: px2rem(20);
              width: px2rem(20);
              border-radius: 50%;
              background: white;
              box-shadow: 0 4px 4px 0 rgba(0, 0, 0, .15);
              border: px2rem(1) solid #ddd;
            }
          }
        }
        .text-wrapper {
          position: absolute;
          left: 0;
          bottom: px2rem(10);
          width: 100%;
          color: #333;
          font-size: px2rem(12);
          text-align: center;
        }
      }
  }
</style>
