<template>
  <div class="scroll-wrapper"
    :class="{'no-scroll': ifNoScroll}"
    @scroll.passive="handleScroll"
    ref="scrollWrapper">
    <slot></slot>
  </div>
</template>

<script>
  import { realPx } from '@/utils/utils'

  export default {
    props: {
      top: {
        type: Number,
        default: 0
      },
      bottom: {
        type: Number,
        default: 0
      },
      ifNoScroll: {
        type: Boolean,
        default: false
      }
    },
    methods: {
      handleScroll(e) {
        const offsetY = e.target.scrollTop || window.pageYOffset || document.body.scrollTop
        this.$emit('onScroll', offsetY)
      },
      scrollTo(x, y) {
        this.$refs.scrollWrapper.scrollTo(x, y)
      },
      refresh() {
        if (this.$refs.scrollWrapper) {
          // 计算滚动条高度
          this.$refs.scrollWrapper.style.height = window.innerHeight - realPx(this.top) - realPx(this.bottom) + 'px'
          console.log(this)
          console.log(this.top)
          console.log(this.bottom)
          this.$refs.scrollWrapper.addEventListener('scroll', this.handleScroll)
        }
      }
    },
    mounted() {
      this.refresh()
    }
  }
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global";

  .scroll-wrapper {
    position: relative;
    z-index: 100;
    width: 100%;
    overflow-x: hidden;
    overflow-y: scroll;
    -webkit-overflow-scrolling: touch;
    &::-webkit-scrollbar {
      display: none;
    }
    &.no-scroll {
      overflow: hidden;
    }
  }
</style>
