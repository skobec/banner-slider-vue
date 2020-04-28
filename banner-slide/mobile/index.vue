<template>
  <div class="wrap">
    <div class="index__slide" :class="{white__index: isColorText}">
      <span aria-hidden="true" class="this__index__slide">{{activeItemIndex + 1}}</span><!--
      --><span aria-hidden="true" class="length__slider">/{{items.length}}</span>
    </div>

    <div id="swiper-block" class="swiper" :class="{ init: init }">
      <div
        v-for="(item, index) in items"
        :key="index"
        class="swiper-item"
        :class="{ black__block: item.textColor === 2, special__banner: item.isSpecialMobile}"
        :style="{ backgroundColor: item.backgroundColor }"
      >
        <div
          class="slide__banner slide__banner__one"
          :class="{slide__banner__two : item.advantages.length, slide__banner__three: !item.description && !item.advantages.length}">
          <div>
            <text-title class="slide__title" :text="item.title"/>
            <div v-if="item.advantages.length && !item.isSpecialMobile" class="worth__list">
              <div v-for="item in item.advantages" :key="item.index" class="worth__list__option">
                <text-main-title class="percent" :text="item.title"/>
                <text-desc class="info" :text="item.description"/>
              </div>
            </div>
            <div v-if="item.description.length && !item.isSpecialMobile" class="worth__list worth__description">
              <text-mini-acent class="text__desc" :text="item.description"/>
            </div>
            <!--<span @click="openLinkClicked" href="#" v-html="item.targetLinkTitle"/>-->
            <button-form
              v-if="!item.isSpecialMobile && item.targetLinkTitle"
              class="button__slider"
              :name="item.targetLinkTitle"
              size="s"
              :color="colorBtn"
              @click="openLinkClicked(item)"/>
          </div>
        </div>
        <div
          class="banner__block"
          :class="{
            banners__layout__two : item.imagePosition === 'center',
            banners__layout__one : item.imagePosition === 'bottom'
          }"
        >
          <image-item
            class="slide__image"
            :imageSrc="item.imageUrlMobile"
            :loadImageSlide="isActiveSlideIndex(index) || index === activeItemIndex + 1"
          />

        </div>
        <div
          :colorBtn="!privateBtn ? 'neon' : 'gold' "
          @click="openLinkClicked(item)"
          v-if="item.isSpecialMobile"
          class="btn__link">
          {{ item.targetLinkTitle }}
        </div>
      </div>
    </div>

  </div>
</template>


<script>
  import Utils from '~/plugins/utils.js'
  import ImageItem from './image-item'

  let Swiper

  if (process.browser) {
    Swiper = require('siema')
  }

  export default {
    name: 'MobileSlider',
    components: {
      'image-item': ImageItem
    },
    props: {
      privateBtn: {
        type: Boolean,
        default: false
      },
      colorBtn: {
        type: String,
        default: 'neon'
      }
    },
    data () {
      return {
        activeItemIndex: 0,
        sliderIntervalId: null,
        init: false
      }
    },
    mounted () {

      if (!this.items.length) {
        return
      }

      this.startSliderInterval()

      this.swiper = new Swiper({
        selector: '#swiper-block',
        onInit: this.onInitSwiper,
        onChange: this.onChangeSwiper,
        duration: 350,
        loop: true
      })

      window.addEventListener('visibilitychange', this.handleVisibilityChange)
    },
    beforeDestroy () {
      window.removeEventListener('visibilitychange', this.handleVisibilityChange)
    },
    computed: {
      isColorText () {
        const whiteContent = 2

        const activeItem = this.items[this.activeItemIndex]

        return activeItem && activeItem.textColor === whiteContent
      },
      items () {
        return this.$store.state.pages.main.banners || []
      },
      intervalTime () {
        const item = this.items[this.activeItemIndex]

        return item ? (item.duration || 5) * 1000 : 5000
      }
    },
    methods: {
      onInitSwiper () {
        this.init = true
      },
      handleVisibilityChange () {
        if (document.hidden) {
          clearTimeout(this.sliderIntervalId)
        } else {
          this.nextSlide()
          this.startSliderInterval()
        }
      },
      isActiveSlideIndex (index) {
        return index === this.activeItemIndex
      },
      goToSlide (index) {
        if (!this.isActiveSlideIndex(index)) {
          this.startSliderInterval()

          this.swiper.goTo(index)
        }
      },
      onChangeSwiper () {
        this.activeItemIndex = this.swiper.currentSlide
        this.startSliderInterval()
      },
      startSliderInterval () {
        if (this.sliderIntervalId) {
          clearTimeout(this.sliderIntervalId)
        }

        this.sliderIntervalId = setTimeout(this.nextSlide, this.intervalTime)
      },
      nextSlide () {
        this.swiper.next()
      },
      openLinkClicked (item) {
        const activeSlideLink = item.targetLink

        if (Utils.isSelfPage(activeSlideLink)) {
          Router.go(activeSlideLink)
        } else {
          Router.goAnotherSite(activeSlideLink)
        }
      }
    }
  }
</script>

<style scoped lang="sass">
  @import '~assets/css/variables.scss'

  .wrap
    position: relative

  .swiper
    height: 240px
    opacity: 0
    transition: opacity 0.3s
    &.init
      opacity: 1

  .swiper-item
    height: 240px
    display: flex
    position: relative

  .banner__block
    height: 215px
    width: 204px
    position: absolute
    top: 0
    right: 0

  /*<!--special banner-->*/
  .special__banner
    flex-direction: column
    justify-content: space-between
    text-align: center
    .slide__banner
      height: auto
      width: 100%
      padding-left: 0
      .slide__title
        margin-top: 16px
        margin-bottom: 24px

    .banner__block
      height: 70%
      width: auto
      margin: inherit
      position: relative
      .slide__image /deep/ img
        position: absolute
        top: 0
        bottom: 0
        right: 0
        left: 0
        margin: 0 auto
        height: 100%
        width: auto

    .btn__link
      position: relative
      display: initial
      border-radius: 4px
      background: transparent
      padding: 17px 0
      line-height: 12px
      font-size: 13px
      color: $grey-600
      cursor: pointer
      width: auto
      margin: 0 auto
      bottom: 0

    &.black__block
      .btn__link
        color: #ffffff
  /*<!--special banner-->*/

  .slide__title
    transition: transform 0.65s, color 0.65s

  .worth__list
    transition: transform 0.7s, color 0.7s

  .button__slider
    transition: transform 0.8s, color 0.8s

    .banner__block
      transition: transform 0.9s, color 0.9s

  .button__slider
    position: absolute
    bottom: 24px
    left: 16px

  .button__slider /deep/ .shadow__button
      display: none

  .button__slider /deep/ button.button
    height: 36px
    line-height: 36px
    width: auto!important

  .button__slider /deep/ .button__name.small
    font-size: 14px
    line-height: 36px

  .slide__banner
    display: flex
    flex-direction: row
    z-index: 1
    padding-left: 16px
    position: relative
    height: 100%
    color: #10002b
    font-size: 20px
    & > div
      flex: 1
    .worth__list
      margin-bottom: 56px
    .slide__title
      font-size: 18px
      margin-bottom: 8px
      margin-top: 16px
      word-break: break-word
    .text__desc
      font-size: 13px
      line-height: 1.38
      letter-spacing: normal

  .slide__banner__two
    width: 45%
    .slide__title
      font-size: 16px
    .worth__list
      margin-bottom: 0

  .banners__layout__one
    bottom: 0
    top: auto
    .slide__image /deep/ img
      position: absolute
      right: 0
      bottom: 0
      width: 100%

  .banners__layout__two
    top: 0
    bottom: 0
    margin: auto 0
    .slide__image /deep/ img
      position: absolute
      top: 0
      bottom: 0
      right: 0
      margin: auto 0
      width: 100%

  .slide__banner__three
    display: flex
    flex-direction: row
    align-items: center
    .slide__title
      font-size: 16px
      margin-bottom: 48px

  .worth__list__option
    margin-bottom: 48px
    margin-right: 10px
    &:nth-child(3)
      display: none
    .percent
      font-size: 14px
      font-weight: 500
      font-style: normal
      font-stretch: normal
      line-height: 1.43
      letter-spacing: normal
      color: #10002b
      opacity: 0.6
      margin-bottom: 3px
    .info
      font-size: 12px
      font-weight: normal
      font-style: normal
      font-stretch: normal
      line-height: 1.33
      letter-spacing: normal
      color: #10002b

  .worth__list
    display: flex

  .black__block
    color: #ffffff
    .button__slider,.worth__list__option .percent,.worth__list__option .info, .slide__title,.text__desc
      color: #ffffff

  .slide__banner
    width: 47%

  .index__slide
    position: absolute
    top: 12px
    right: 16px
    background: rgba(0,0,0, .1)
    border-radius: 40px
    padding: 4px 6px
    z-index: 10
    font-size: 14px

  .index__slide.white__index
    background: rgba(255,255,255, .1)
    color: rgba(255,255,255, .8)
</style>
