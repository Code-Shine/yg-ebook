<template>
  <transition name="slide-up">
    <div class="setting-wrapper" v-show="menuVisible" && settingVisible>
      <div class="setting-theme">
        <div class="setting-theme-item" v-for="(item, index) in themeList" :key="index" @click="setTheme(index)">
          <div class="preview" :style="{background: item.style.body.background}" :class="{'no-border': item.style.body.background !== '#fff'}"></div>
          <div class="text" :class="{'selected': index === defaultTheme}">{{item.name}}</div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>

export default {}

</script>

<style lang='scss' rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global.scss";
</style>
