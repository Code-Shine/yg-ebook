<template>
  <div class="shelf-list">

        <div class="shelf-list-item-wrapper" v-for="item in shelfList" :key="item.id">
          <shelf-item :data="item" :style="{height: itemHeight}"></shelf-item>
          <!-- 显示图书的标题 -->
          <div class="shelf-list-title-wrapper">
            <span class="shelf-list-title title-small">{{item.title}}</span>
          </div>
        </div>
  </div>

</template>

<script>
import { storeShelfMixin } from '../../utils/mixin'
import ShelfItem from './ShelfItem'
import { realPx } from '../../utils/utils'
export default {
  mixins: [storeShelfMixin],
  components: {
      ShelfItem
    },
    computed: {
      itemHeight() {
        // w / 250 = h / 350
        // h = w / 250 * 350
        return ((window.innerWidth - realPx(120)) / 3) / 250 * 350 + 'px'
        // return 100 + 'px'
      }
    }
}
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global";
  .shelf-list {
    position: absolute;
    top: px2rem(180);
    bottom: px2rem(20);
    left: 0;
    z-index: 100;
    width: 100%;
    display: flex;
    flex-flow: row wrap;
    padding: 0 px2rem(15);
    box-sizing: border-box;
    .shelf-list-item-wrapper{
      flex: 0 0 33.33%;
      width: 33.33%;
      padding: px2rem(15);
      box-sizing: border-box;
    }
  }
</style>
