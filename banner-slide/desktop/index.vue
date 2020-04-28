<template>
  <v-container bg="gray" class="wrp__slides" v-if="items.length">
    <div class="slider__container" :class="{private__slider__container : privateBtn}">
      <div class="slide__items" :style="{backgroundColor: items[activeItemIndex].backgroundColor}">
        <transition-group name="slide">
          <slide-item
            v-for="(item, index) in items"
            :item="item"
            :class="moveType"
            :key="item.id"
            v-show="activeItemIndex === index"
            :loadImageSlide="activeItemIndex === index || activeItemIndex+1 === index"
            :colorBtn="!privateBtn ? 'neon' : 'gold' " />
        </transition-group>
      </div>

      <div aria-label="Предыдущий слайд" tabindex="0" role="button" class="arrow__scroll__left" :class="{white__arrow: isColorText}" @keydown.enter="prevSlide" @click="prevSlide" v-html="require('~/assets/img/arrow-scroll.svg')"></div>
      <div aria-label="Следующий слайд" tabindex="0" role="button" class="arrow__scroll__right" :class="{white__arrow: isColorText}" @keydown.enter="nextSlide" @click="nextSlide" v-html="require('~/assets/img/arrow-scroll.svg')"></div>

      <div class="pin_sliders" :class="{white__pin: isColorText}" aria-hidden="true">
        <div class="pin" v-for="(item, index) in items" @click="goToSlide(index)">
          <loader :time="intervalTime" :active="isActiveSlideIndex(index)" />
        </div>
      </div>
      <div class="index__slide" :class="{white__index: isColorText}" :aria-label="`Текущий слайд ${activeItemIndex + 1} из ${slideLength}`">
        <span aria-hidden="true" class="this__index__slide">{{activeItemIndex + 1}}</span><!--
        --><span aria-hidden="true" class="length__slider">/{{slideLength}}</span>
      </div>
    </div>

  </v-container>
</template>

<script>
  import SlideItem from './slide-item'
  import Loader from './loader'

  export default {
    name: 'DesktopSlider',
    components: {
      'slide-item': SlideItem,
      loader: Loader
    },
    props: {
      privateBtn: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        moveType: null,
        activeItemIndex: 0,
        slideDirection: {
          next: 'next',
          back: 'back'
        },
        sliderIntervalId: null
      }
    },
    mounted () {
      this.startSliderInterval()

      window.addEventListener('visibilitychange', this.handleVisibilityChange)
    },
    beforeDestroy () {
      window.removeEventListener('visibilitychange', this.handleVisibilityChange)
    },
    computed: {
      isColorText () {
        const whiteContent = 2

        return this.items[this.activeItemIndex].textColor === whiteContent
      },
      items () {
        return this.$store.state.pages.main.banners
      },
      slideLength () {
        return this.items.length
      },
      intervalTime () {
        const item = this.items[this.activeItemIndex]

        return item ? (item.duration || 5) * 1000 : 5000
      }
    },
    methods: {
      handleVisibilityChange () {
        if (document.hidden) {
          clearTimeout(this.sliderIntervalId)
        } else {
          this.nextSlide()
        }
      },
      startSliderInterval () {
        if (this.sliderIntervalId) {
          clearTimeout(this.sliderIntervalId)
        }

        this.sliderIntervalId = setTimeout(this.nextSlide, this.intervalTime)
      },
      isActiveSlideIndex (index) {
        return index === this.activeItemIndex
      },
      goToSlide (index, direction) {
        if (!this.isActiveSlideIndex(index)) {
          this.moveType = direction || (index < this.activeItemIndex ? this.slideDirection.back : this.slideDirection.next)
          this.activeItemIndex = index
          this.startSliderInterval()
        }
      },
      nextSlide () {
        const nextSlideIndex = this.activeItemIndex + 1 === this.slideLength ? 0 : this.activeItemIndex + 1

        this.goToSlide(nextSlideIndex, this.slideDirection.next)
      },
      prevSlide () {
        const nextSlideIndex = this.activeItemIndex - 1 < 0 ? this.slideLength - 1 : this.activeItemIndex - 1

        this.goToSlide(nextSlideIndex, this.slideDirection.back)
      }
    }
  }
</script>

<style lang="sass" scoped>
  @import '~assets/css/variables.scss'

  .slide
    position: absolute

  .slide__items
    overflow: hidden
    position: relative
    width: 100%
    height: 100%
    transition: 0.6s background-color

  .index__slide
    position: absolute
    top: 41px
    right: 41px
    z-index: 1
    &.white__index
      .this__index__slide,.length__slider
        color: rgba(255, 255, 255, 0.45)

    span
      font-size: 18px
      font-weight: normal
      font-style: normal
      font-stretch: normal
      line-height: 1.33
      letter-spacing: normal
      color: $grey-600

  .arrow__scroll__left
    height: 40px
    width: 40px
    position: absolute
    margin: auto 0
    top: 0
    bottom: 0
    z-index: 4
    left: 36px
    text-align: center
    transition: all .2s ease
    cursor: pointer
    &:after
      content: ""
      height: 40px
      width: 40px
      position: absolute
      left: 0
      top: 0
      opacity: 0.5
      background-color: rgba(16,0,43,0.1)
      border-radius: 50%
      transition: all .2s ease
    & /deep/ svg
      transform: rotate(180deg)
      position: absolute
      margin: auto
      top: 0
      bottom: 0
      left: 0
      right: 0
      z-index: 1
      width: 8px
      height: 24px
    &:hover
      &:after
        transform: scale(1.1)
        background-color: rgba(16,0,43,0.15)

  .arrow__scroll__right
    height: 40px
    width: 40px
    position: absolute
    margin: auto 0
    top: 0
    bottom: 0
    z-index: 4
    right: 36px
    text-align: center
    transition: all .2s ease
    cursor: pointer
    &:after
      content: ""
      height: 40px
      width: 40px
      position: absolute
      left: 0
      top: 0
      opacity: 0.5
      background-color: rgba(16,0,43,0.1)
      border-radius: 50%
      transition: all .2s ease
    & /deep/ svg
      position: absolute
      margin: auto
      top: 0
      bottom: 0
      left: 0
      right: 0
      z-index: 1
      width: 8px
      height: 24px
    &:hover
      &:after
        transform: scale(1.1)
        background-color: rgba(16,0,43,0.15)

  .arrow__scroll__left,.arrow__scroll__right
    & /deep/ svg
      & path
        transition: 0.3s stroke

  .white__arrow
    &.arrow__scroll__left,&.arrow__scroll__right
      &:after
        background-color: rgba(255,255,255,0.1)
      &:hover
        &:after
          background-color: rgba(255,255,255,0.15)

      & /deep/ svg
        & path
          transition: 0.3s stroke
          stroke: #fff

  .slider__container
    height: 540px
    width: 100%
    position: relative

  .pin_sliders
    position: absolute
    bottom: 33px
    left: 0
    right: 0
    margin: 0 auto
    display: flex
    justify-content: center
    z-index: 1
    .pin
      margin: 0 12px
      width: 10px
      &:hover
        & /deep/ .circle
          transition: 0.3s border-color, 0.3s transform
          transform: scale(1.4)
          border-width: 1px

  @media all and (max-width: 1000px)
    .pin
      & /deep/ .passive__circle
        stroke: #999999
      & /deep/ .circle
        border-color: #999999

    .white__pin
      .pin
        & /deep/ .passive__circle
          stroke: #999999
        & /deep/ .circle
          border-color: #999999

    .slider__container
      height: 450px

  @media all and (min-width: $tablet-width)
    .white__pin
      & .pin
        & /deep/ .passive__circle
          stroke: rgba(255,255,255,0.45)
        & /deep/ .circle
          border-color: rgba(255,255,255,0.45)
        & /deep/ .active-circle
          stroke: #ffffff

      & .pin_sliders
        & .pin
          &:hover
            & /deep/ .circle
              border-color: rgba(255,255,255,1)

  @media all and (max-width: $tablet-width)
    .pin_sliders
      display: none
    .index__slide
      top: 16px
    .arrow__scroll__left
      margin: 0
      top: inherit
      bottom: 20px
      right: 84px
      left: inherit
    .arrow__scroll__right
      margin: 0
      top: inherit
      bottom: 20px
      right: 24px
    .pin_sliders
      bottom: 13px
      z-index: 2
    .slider__container
      min-height: 352px
      &.private__slider__container
        background-color: white
        padding-bottom: 45px
        margin-bottom: 0
        .pin_sliders
          bottom: -5px

  @media (max-width: $phone-width)
    .index__slide
      display: none

    .slider__container
      margin-bottom: 45px
      height: 550px
      &.private__slider__container
        background-color: white
        padding-bottom: 65px
        margin-bottom: 0
        .arrow__scroll__left,
        .arrow__scroll__right
          bottom: -5px
        .pin_sliders
          bottom: 10px

</style>
