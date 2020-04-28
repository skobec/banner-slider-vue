<template lang="html">
  <div class="slide__item" :class="{ black__block: item.textColor === 2}">
    <v-grid class="slide__grid" :padding="true" :vpadding="false">
      <div class="slide__banner slide__banner__one" :class="{slide__banner__two : item.advantages.length, slide__banner__three: !item.description && !item.advantages.length}">
        <div>
          <text-banner class="slide__title" :text="item.title" />
          <div v-if="item.advantages.length" class="worth__list">
            <div v-for="item in item.advantages" :key="item.index" class="worth__list__option">
              <text-main-title class="percent" :text="item.title" />
              <text-desc class="info" :text="item.description" />
            </div>
          </div>
          <div v-if="item.description.length" class="worth__list worth__description">
            <text-mini-acent :text="item.description" />
          </div>
          <button-form
            v-if="item.targetLinkTitle"
            :ariaLabel="`${item.title} ${item.targetLinkTitle}`"
            class="button__slider"
            :name="item.targetLinkTitle"
            size="l"
            :color="colorBtn"
            @click="openLinkClicked"
          />
        </div>
      </div>
      <div
        class="banner__block"
        :class="{
          banners__layout__two : item.imagePosition === 'center',
          banners__layout__one : item.imagePosition === 'bottom'
        }"
      >
        <v-picture
          aria-hidden="true"
          class="slide__image"
          :pcImage="pcImageSrc"
          :lazy="false"
        />
      </div>
      <span class="shadow_bl" :style="bgShadow"></span>
    </v-grid>
  </div>

</template>

<script>
  import Utils from '~/plugins/utils.js'
  import VPicture from '~/components/ui-kit/v-picture'

  export default {
    name: 'SlideItem',
    components: {
      'v-picture': VPicture
    },
    props: {
      item: {
        type: Object,
        title: String, // Заголовок
        description: String, // Описание
        advantages: Array, // Список преимуществ
        backgroundColor: String, // Цвет фона
        percent: String, // Процент преимущества
        info: String, // Описание преимущества
        imageUrlWeb: String, // Ссылка на баннер
        position: String // Расположение баннера (тип баннера)
      },
      colorBtn: {
        type: String,
        default: 'neon'
      },
      loadImageSlide: { // загрузить картинку или нет
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        pcImageSrc: this.loadImageSlide ? this.item.imageUrlWeb : '' // картинку загружаем когда активный слайд
      }
    },
    computed: {
      bgShadow () {
        return `background-image: linear-gradient(to top, ${this.item.backgroundColor}, rgba(204, 212, 212, 0))`
      }
    },
    methods: {
      setImageSrc () {
        this.pcImageSrc = this.item.imageUrlWeb
      },
      openLinkClicked () {
        const activeSlideLink = this.item.targetLink

        if (Utils.isSelfPage(activeSlideLink)) {
          Router.go(activeSlideLink)
        } else {
          Router.goAnotherSite(activeSlideLink)
        }
      }
    },
    watch: {
      loadImageSlide (value) {
        if (value) {
          this.setImageSrc()
        }
      }
    }
  }
</script>

<style lang="sass" scoped>
  @import '~assets/css/variables.scss'

  .slide__title
    transition: transform 0.65s, color 0.65s
  .worth__list
    transition: transform 0.7s, color 0.7s

  .button__slider
    transition: transform 0.8s, color 0.8s

  .banner__block
    transition: transform 0.9s, color 0.9s

  .slide-enter-active
    transition: transform 1.9s, opacity 1s

  .slide-leave-active
    transition: transform 0.8s, opacity .4s

  .slide-enter.next
    opacity: 0
    .slide__title,.worth__list,.button__slider,.banner__block
      transform: translateX(100vw)

  .slide-leave-to.next
    opacity: 0
    .slide__title,.worth__list,.button__slider,.banner__block
      transform: translateX(-100vw)

  .slide-enter.back
    opacity: 0
    .slide__title,.worth__list,.button__slider,.banner__block
      transform: translateX(-100vw)

  .slide-leave-to.back
    opacity: 0
    .slide__title,.worth__list,.button__slider,.banner__block
      transform: translateX(100vw)

  .slide__banner
    display: flex
    flex-direction: row
    align-items: center
    z-index: 1
    & > div
      flex: 1
    .worth__list
      margin-bottom: 56px
    .slide__title
      font-size: 64px
      margin-bottom: 16px
      word-break: break-word

  .slide__banner__two
    .slide__title
      font-size: 48px
    .worth__list
      margin-bottom: 0

  .banners__layout__one
    position: relative
    margin-left: auto
    .slide__image /deep/ img
      position: absolute
      right: 0
      bottom: 0
      height: 100%

  .banners__layout__two
    position: relative
    margin-left: auto
    .slide__image /deep/ img
      position: absolute
      top: 0
      bottom: 0
      right: 0
      margin: auto 0
      height: 100%

  .slide__banner__three
    display: flex
    flex-direction: row
    align-items: center
    .slide__title
      font-size: 36px
      margin-bottom: 48px

  .worth__list__option
    margin-bottom: 48px
    margin-right: 10px
    .percent
      font-size: 32px
      font-weight: 500
      font-style: normal
      font-stretch: normal
      line-height: 1.25
      letter-spacing: normal
      color: $grey-600
      margin-bottom: 8px
    .info
      font-style: normal
      font-stretch: normal
      line-height: 1.5
      letter-spacing: normal
      color: #10002b

  .shadow_bl
    display: none
    opacity: 0.7
    width: 100%
    height: 100px
    position: absolute
    bottom: 0
    left: 0
    background-image: linear-gradient(to top, #ccd4d4, rgba(204, 212, 212, 0))
  .worth__list
    display: flex
    margin-top: 24px

  .slide__item
    height: 100%
    box-sizing: border-box
    position: absolute
    left: 0
    top: 0
    width: 100%
    z-index: 1
    &.active
      z-index: 3
    .slide__banner
      padding: 64px 0
    &.black__block
      color: #ffffff
      .button__slider,.worth__list__option .percent,.worth__list__option .info
        color: #ffffff

  .slide__banner
    width: 47%

  .slide__grid
    height: 100%
    display: flex

  @media all and (max-width: 1300px)
    .slide__grid
      width: 95%

  @media all and (max-width: $tablet-width)
    .slide__banner
      .slide__title
        font-size: 56px
        margin-bottom: 8px
      .worth__list
        margin-bottom: 32px
    .slide__banner__two
      .slide__title
        font-size: 46px
        margin-bottom: 16px
      .worth__list
        margin-bottom: 32px
        .worth__list__option
          margin-bottom: 0
          .percent
            font-size: 24px
          .info
            font-size: 16px
    .slide__banner__three
      .slide__title
        font-size: 36px
        margin-bottom: 48px

    .banners__layout__two
      width: 40%
      .slide__image /deep/ img
        height: auto
        width: 100%

    .banners__layout__two /deep/ .button__slider,
    .banners__layout__two /deep/ .button.large,
    .banners__layout__two /deep/ .button__name.large
      height: 56px!important
      line-height: 56px!important
      padding: 0 12px

    .worth__list
      margin-top: 16px

    .worth__list__option
      margin-bottom: 32px

    .slide__item
      .slide__banner
        padding: 34px 0

    .slide__banner
      width: 60%

    .banners__layout__one
      height: auto
      width: 45%
      .slide__image /deep/ img
        width: 100%
        height: auto

  @media (max-width: $phone-width)
    .shadow_bl
      display: block
    .slide__banner
      flex-direction: column
      .slide__title
        margin-bottom: 4px

    & /deep/ .slide__title
      font-size: 24px!important
      font-weight: bold!important

    & /deep/ .button__slider
      display: inline-block!important
      width: auto
      bottom: 22px
      left: 0
      right: 0
      margin: 32px auto 0

    .worth__list
      margin-top: 8px
      &.worth__description
        display: block

    .worth__list__option
      margin-bottom: 15px
      .percent
        font-size: 18px
        margin-bottom: 2px

    .slide__banner
      width: 100%
      text-align: center

    .slide__grid
      flex-direction: column
      justify-content: flex-end

    .banner__block
      width: 50%
      margin: 0 auto

    .banners__layout__one
      text-align: center
      .slide__image /deep/ img
        position: relative
        display: inherit
        width: 83%
        margin: 0 auto

    .banners__layout__two
      text-align: center
      padding: 15px 0 0
      .slide__image /deep/ img
        position: relative
        width: 80%

    .slide__banner__two
      .worth__list
        margin-bottom: 8px
        .worth__list__option
          margin-bottom: 0
          .percent
            font-size: 18px
          .info
            font-size: 13px

    .banner__block
      width: 100%

    .banners__layout__one
      position: absolute
      bottom: 0
      left: 0
      right: 0
      margin: 0 auto

    .banners__layout__two
      bottom: 60px

    .slide__grid
      display: block

    & /deep/ .button__slider
      position: absolute!important
      padding: 0 $phone-padding
      z-index: 5
  @media (max-width: 550px)
    .banners__layout__one,.banners__layout__two
      .slide__image /deep/ img
        width: 100%
  @media (max-width: 400px)
    .banner__block
      width: 100%

    .banners__layout__one
      position: absolute
      bottom: 0
      left: 0
      right: 0
      margin: 0 auto
      .slide__image /deep/ img
        position: relative
        display: inherit
        width: 100%
        margin: 0 auto

    .banners__layout__two
      bottom: 60px
      .slide__image /deep/ img
        position: relative
        width: 100%

    .slide__grid
      display: block

    & /deep/ .button__slider
      position: absolute!important
      padding: 0 $phone-padding
      z-index: 5

</style>
