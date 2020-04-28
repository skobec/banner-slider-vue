<template>
  <div class="wrap-loader">
    <transition name="fade">
      <svg v-show="active" width="14" height="14" viewBox="0 0 120 120" class="loader__svg">
        <circle class="passive__circle" cx="60" cy="60" r="54" fill="none" stroke="#999999" stroke-width="6" />
        <circle
          class="active-circle"
          :style="{ 'animation-duration': `${time}ms` }"
          cx="60" cy="60" r="54" />
      </svg>
    </transition>
    <transition name="fade">
      <div v-show="!active" class="circle" />
    </transition>
  </div>
</template>

<script>
  export default {
    name: 'Loader',
    props: {
      active: Boolean,
      time: Number // ms
    }
  }
</script>

<style scoped lang="sass">
  .wrap-loader
    position: relative

  .passive__circle
    stroke-width: 10px

  .fade-enter-active, .fade-leave-active
    transition: opacity .1s

  .fade-enter, .fade-leave-to
    opacity: 0

  .loader__svg
    margin: 0 -2px
    position: absolute
    top: -2px
    transform: rotate(-85deg)

  .active-circle
    animation-duration: 0ms
    animation-fill-mode: forwards
    animation-timing-function: linear
    fill: none
    stroke: #10002b
    stroke-width: 14
    stroke-dasharray: 340
    stroke-dashoffset: 170
    stroke-linecap: round
    animation-name: progress

  @keyframes progress
    from
      stroke-dashoffset: 340
    to
      stroke-dashoffset: 0

  .circle
    width: 8px
    height: 8px
    cursor: pointer
    border: 1px solid rgba(16,0,43,0.20)
    border-radius: 50%
    transition: 0.3s border-color, 0.3s transform

</style>
