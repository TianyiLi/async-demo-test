<template functional>
  <div class="progress-bar-wrapper">
    <div class="progress-bar"
      :style="{width: `${(value / maxiumAmount * 100).toFixed(2)}%`}">
      <span class="progress-text">
        {{(value / maxiumAmount * 100).toFixed(2) + '%'}}
      </span>
    </div>
  </div>
</template>
<script>
export default {
  props: ['value', 'maxiumAmount'],
  mounted() {
    console.log(this.value, this.maxiumAmount)
  }
}
</script>
<style lang="stylus" scoped>
.progress-bar-wrapper
  height 26px
  background-color #ebeef5
  border-radius 13px
  overflow hidden
  .progress-bar
    height 100%
    background-color #409eff
    text-align right
    border-radius 13px
    .progress-text
      color white
      font-size 12px
      margin 0 5px
      &:after
        content ''
        height 100%
        vertical-align middle
</style>