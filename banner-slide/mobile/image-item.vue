<template lang="html">
  <v-picture
    :mobileImage="mobileImageSrc"
    :lazy="false"
  />
</template>

<script>
  import VPicture from '~/components/ui-kit/v-picture'

  export default {
    name: 'ImageItem',
    components: {
      'v-picture': VPicture
    },
    props: {
      imageSrc: String,
      loadImageSlide: { // загрузить картинку или нет
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        mobileImageSrc: this.loadImageSlide ? this.imageSrc : '' // картинку загружаем когда активный слайд
      }
    },
    watch: {
      loadImageSlide (value) {
        if (value) {
          this.mobileImageSrc = this.imageSrc
        }
      }
    }
  }
</script>

<style lang="sass" scoped>
  @import '~assets/css/variables.scss'
</style>
