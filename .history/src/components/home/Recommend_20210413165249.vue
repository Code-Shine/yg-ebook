<template>
  <div class="recommend">
    <!-- <title-view :label="$t('home.recommend')" :btn="$t('home.seeAll')"></title-view> -->
    <div class="title">
      <span class="push">小谢推荐</span>
    </div>
    <div class="recommend-list">
      <div class="recommend-item" v-for="(item, index) in data" :key="index" @click="showBookDetail(item)">
        <div class="img-wrapper">
          <img class="img" :src="item.cover">
        </div>
        <div class="content-wrapper">
          <div class="title title-medium" ref="title">{{item.title}}</div>
          <div class="num sub-title" ref="num">{{$t('home.readers').replace('$1', item.readers)}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  // import TitleView from './Title'
  import { storeHomeMixin } from '../../utils/mixin'

  export default {
    mixins: [storeHomeMixin],
    components: {
      // TitleView
    },
    props: {
      data: Array
    }
  }
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global";

  .recommend {
    .title{
      width: 100%;
      padding: px2rem(15) px2rem(15);
      box-sizing: border-box;
      font-size: px2rem(18);
      color: #333;
      font-weight: bold;
    }
    .recommend-list {
      width: 100%;
      @include top;
      padding: 0 px2rem(5);
      box-sizing: border-box;
      .recommend-item {
        flex: 0 0 33.33%;
        width: 33.33%;
        padding: 0 px2rem(5);
        box-sizing: border-box;
        .img-wrapper {
          @include center;
          .img {
            width: 80%;
          }
        }
        .content-wrapper {
          width: 100%;
          margin-top: px2rem(10);
          @include columnCenter;
          .title {
            text-align: center;
          }
          .num {
            margin-top: px2rem(5);
          }
        }
      }
    }
  }
</style>
