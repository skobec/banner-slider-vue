<template lang="html">
  <div>
    <desktop-slider :privateBtn="privateBtn" class="desktop" />
    <mobile-slider :colorBtn="!privateBtn ? 'neon' : 'gold'" class="mobile" />
  </div>
</template>

<script>
  import desktopSlider from './desktop/index.vue'
  import mobileSlider from './mobile/index.vue'

  export default {
    name: 'SlideBannerTypeEffect',
    components: {
      'desktop-slider': desktopSlider,
      'mobile-slider': mobileSlider
    },
    props: {
      privateBtn: {
        type: Boolean,
        default: false
      }
    }
  }
</script>

<style lang="sass" scoped>
  @import '~assets/css/variables.scss'

  .mobile
    display: none

  @media (max-width: $phone-width)
    .mobile
      display: block
    .desktop
      display: none


</style>
