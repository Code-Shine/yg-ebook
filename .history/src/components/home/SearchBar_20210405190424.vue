<template>
  <div>
    <div class="search-bar">
      <!-- 标题栏动画 -->
      <transition name="title-move">
        <div class="search-bar-title-wrapper" v-show="titleVisible">
          <div class="title-text-wrapper">
            <span class="title-text title">{{$t('home.title')}}</span>
          </div>
          <div class="title-icon-shake-wrapper" @click="showFlapCard">
            <span class="icon-shake icon"></span>
          </div>
        </div>
      </transition>

      <!-- 返回图标栏 -->
      <div class="title-icon-back-wrapper" :class="{'hide-title': !titleVisible}" @click="back">
        <span class="icon-back icon"></span>
      </div>

      <!-- 搜索输入栏 -->
      <div class="search-bar-input-wrapper" :class="{'hide-title': !titleVisible}">
        <div class="search-bar-blank" :class="{'hide-title': !titleVisible}"></div>
        <div class="search-bar-input">
          <span class="icon-search icon"></span>
          <input class="input"
                 type="text"
                 :placeholder="$t('home.hint')"
                 v-model="searchText"
                 @click="showHotSearch"
                 @keyup.13.exact="search">
        </div>
      </div><!-- end search-bar-input-wrapper -->
    </div> <!-- end search-bar -->

    <hot-search-list v-show="hotSearchVisible" ref="hotSearch"></hot-search-list>
  </div>
</template>

<script>
  import { storeHomeMixin } from '../../utils/mixin'
  // import HotSearchList from './HotSearchList'

  export default {
    // components: { HotSearchList },
    mixins: [storeHomeMixin],
    data() {
      return {
        searchText: '',
        titleVisible: true,
        shadowVisible: false,
        hotSearchVisible: false
      }
    },
    watch: {
      offsetY(offsetY) {
        if (offsetY > 0) {
          // 如果y轴的偏移大于0，则隐藏标题栏和阴影
          this.hideTitle()
          this.showShadow()
        } else {
          this.showTitle()
          this.hideShadow()
        }
      },
      hotSearchOffsetY(offsetY) {
        if (offsetY > 0) {
          this.showShadow()
        } else {
          this.hideShadow()
        }
      }
    },
    methods: {
      search() {
        this.$router.push({
          path: '/store/list',
          query: {
            keyword: this.searchText
          }
        })
      },
      showFlapCard() {
        this.setFlapCardVisible(true)
      },
      back() {
        if (this.offsetY > 0) {
          this.showShadow()
        } else {
          this.hideShadow()
        }
        if (this.hotSearchVisible) {
          this.hideHotSearch()
        } else {
          this.$router.push('/store/shelf')
        }
      },
      showHotSearch() {
        this.hideTitle()
        this.hideShadow()
        this.hotSearchVisible = true
        this.$nextTick(() => {
          this.$refs.hotSearch.reset()
        })
      },
      hideHotSearch() {
        this.hotSearchVisible = false
        if (this.offsetY > 0) {
          this.hideTitle()
          this.showShadow()
        } else {
          this.showTitle()
          this.hideShadow()
        }
      },
      hideTitle() {
        // 隐藏标题栏就将titleVisible的值设置为false
        this.titleVisible = false
      },
      showTitle() {
        // 显示标题栏
        this.titleVisible = true
      },
      hideShadow() {
        // 隐藏阴影
        this.shadowVisible = false
      },
      showShadow() {
        // 展示阴影
        this.shadowVisible = true
      }
    }
  }
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global";

  .search-bar {
    position: relative;
    z-index: 150;
    width: 100%;
    height: px2rem(94);
    box-shadow: 0 px2rem(2) px2rem(2) 0 rgba(0, 0, 0, .1);
    &.hide-title {
      height: px2rem(52);
    }
    &.hide-shadow {
      box-shadow: none;
    }
    .search-bar-title-wrapper {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: px2rem(42);
      .title-text-wrapper {
        width: 100%;
        height: px2rem(42);
        @include center;
      }
      .title-icon-shake-wrapper {
        position: absolute;
        right: px2rem(15);
        top: 0;
        height: px2rem(42);
        @include center;
      }
    }
    .title-icon-back-wrapper {
      position: absolute;
      left: px2rem(15);
      top: 0;
      z-index: 200;
      height: px2rem(42);
      // transition: height $animationTime $animationType;
      @include center;
      &.hide-title {
        height: px2rem(52);
      }
    }
    .search-bar-input-wrapper {
      position: absolute;
      left: 0;
      top: px2rem(42);
      display: flex;
      width: 100%;
      height: px2rem(52);
      padding: px2rem(10);
      box-sizing: border-box;
      // transition: top $animationTime $animationType;
      &.hide-title {
        top: 0;
      }
      .search-bar-blank {
        flex: 0 0 0;
        width: 0;
        // transition: all $animationTime $animationType;
        &.hide-title {
          flex: 0 0 px2rem(31);
          width: px2rem(31);
        }
      }
      .search-bar-input {
        flex: 1;
        width: 100%;
        background: #f4f4f4;
        border-radius: px2rem(20);
        padding: px2rem(5) px2rem(15);
        box-sizing: border-box;
        border: px2rem(1) solid #eee;
        @include left;
        .icon-search {
          color: #999;
        }
        .input {
          width: 100%;
          height: px2rem(22);
          border: none;
          background: transparent;
          margin-left: px2rem(10);
          font-size: px2rem(12);
          color: #666;
          &:focus {
            outline: none;
          }
          &::-webkit-input-placeholder {
            color: #ccc;
          }
        }
      }
    }
  }
</style>
