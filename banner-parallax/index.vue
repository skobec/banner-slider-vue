<template>
  <div>
    <v-container class="banner__container">
      <v-grid :padding="true" :resetCellVerticalPadding="true" class="grid desktop__grid">
        <v-grid-cell class="grid__cell" :data="{pcoffsetright:0.5,pc:5,pccolumns:2,tablet:5,tabletcolumns:2,phone:4,phonecolumns:1}">
          <div class="banner__title__container" ref="titleWrp">
            <text-banner class="banner__title" ref="bannerTitle" :class="{static: titleFix}" :text="title"/>
          </div>
          <div class="bottom__content" ref="bottomContent">
            <div class="group__product" v-for="(item,index) in productItems" :key="index">
              <text-acent class="title" :text="item.title"/>
              <text-main :text="item.desc"/>
            </div>
          </div>
        </v-grid-cell>
        <v-grid-cell class="grid__cell grid__cell__image" :data="{pc:5, pcoffset:1,pccolumns:2,tablet:3,tabletcolumns:2,phone:4,phonecolumns:1}">
          <div class="parallax__container">
            <span class="product__line" :style="{'background-image': `url(${bgImage})`, 'background-position-y': `${1000+lastPosition}` * -0.012 + `%`}"></span>
            <span class="product__line" :style="{'background-image': `url(${bgImage})`, 'background-position-y': `${4000+lastPosition}` * 0.015 + `%`}"></span>
            <span class="product__line" :style="{'background-image': `url(${bgImage})`, 'background-position-y': `${80+lastPosition}` * -0.019 + `%`}"></span>
          </div>
        </v-grid-cell>
      </v-grid>

      <div class="grid mobile__grid">
        <v-grid :padding="true" :resetCellVerticalPadding="true" :phoneTopPadding="32" :phoneBottomPadding="16">
          <text-banner :text="title" :isText="true"/>
        </v-grid>
        <div class="parallax__mobile">
          <span class="product__line" :style="{'background-image': `url(${bgImage})`, 'background-position-y': `${130+lastPosition}` * 0.03 + `%`}"></span>
        </div>
        <v-grid :padding="true" :resetCellVerticalPadding="true" >
          <div class="group__product" v-for="(item,index) in productItems" :key="index">
            <text-acent class="title" :text="item.title"/>
            <text-main :text="item.desc"/>
          </div>
        </v-grid>
        <div class="parallax__mobile">
          <span class="product__line" :style="{'background-image': `url(${bgImage})`, 'background-position-y': `${80+lastPosition}` * -0.01 + `%`}"></span>
        </div>
      </div>

    </v-container>
  </div>
</template>

<script>
  export default {
    name: 'BannerParallax',
    props: {
      title: {
        type: String,
        default: ''
      },
      productItems: {
        type: Array,
        default: () => [],
        prop: {
          title: String,
          desc: String
        }
      },
      imageLink: {
        type: String,
        default: ''
      }
    },
    data () {
      return {
        lastPosition: 0,
        titleFix: false,
        range: 200
      }
    },
    mounted () {
      window.addEventListener('scroll', this.handleScroll)
    },
    beforeDestroy () {
      window.removeEventListener('scroll', this.handleScroll)
    },
    computed: {
      bgImage () {
        if (!this.imageLink) {
          return ''
        }

        return Router.formUrl(this.imageLink)
      }
    },
    methods: {
      handleScroll () {
        const { bannerTitle, bottomContent, titleWrp } = this.$refs
        const bannerTitleRect = bannerTitle.$el.getBoundingClientRect()
        const titleWrpRect = titleWrp.getBoundingClientRect()
        const bottomContentPosition = bottomContent.getBoundingClientRect().y
        const titleWrpPosition = titleWrpRect.y + window.scrollY
        const bannerTitleHeight = bannerTitleRect.height
        const scrollTopLimit = titleWrpPosition + bannerTitleHeight // 379px
        const contentHeight = bottomContent.getBoundingClientRect().height + 112
        const calcOpacity = -1 + (this.lastPosition - (bottomContent.getBoundingClientRect().height / 2) + this.range) / this.range

        if (bottomContentPosition - contentHeight <= this.lastPosition) {

          $$(bottomContent).css({
            opacity: calcOpacity
          })
        }

        if (calcOpacity > '1') {
          $$(bottomContent).css({
            opacity: '1'
          })
        } else if (calcOpacity < '0.2') {
          $$(bottomContent).css({
            opacity: '0.2'
          })
        }

        if (bottomContentPosition < scrollTopLimit) {
          const top = window.scrollY + bottomContentPosition - scrollTopLimit

          $$(bannerTitle).css({
            position: 'absolute',
            top: `${top}px`
          })

        } else {
          $$(bannerTitle).css({
            position: 'fixed',
            top: 'initial',
            width: `${titleWrpRect.width}px`
          })
        }
        this.lastPosition = window.scrollY
      }
    }
  }
</script>

<style scoped lang="sass">
  @import '~assets/css/variables.scss'

  .mobile__grid
    display: none

  .banner__title__container
    top: 112px
    position: relative

  .banner__title
    position: absolute
    min-width: 450px
    width: 30%
    &.static
      position: static
      display: inline-block

  .bottom__content
    position: absolute
    bottom: 210px
    padding-top: 112px
    opacity: 0.2

  .group__product
    margin-top: 48px
    &:first-child
      margin-top: 0
    .title
      margin-bottom: 8px

  .parallax__container
    display: flex
    justify-content: space-between

  .product__line
    display: inline-block
    width: 108px
    height: 100vh
    background-repeat: repeat-y
    background-size: 100%

  .banner__container
    background-image: linear-gradient(122deg, $grey-100, $grey-250)
    min-height: 1200px
    height: 100vh
    /deep/ .v-row
      height: 100%
    .grid
      height: 100%
      .grid__cell
        height: 100%
        /deep/ .v-cell-padding
          position: relative
          height: 100%
          .v-cell-data
            height: 100%
            .parallax__container
              height: 100%
              .product__line
                height: 100%

  .header
    width: 100%
    height: 50px
    background: red
    position: fixed
    z-index: 1

  .headroom
    position: fixed
    will-change: transform
    transition: transform 200ms linear

  .headroom--pinned
    transform: translateY(0%)

  .headroom--unpinned
    transform: translateY(-100%)

  .parallax__mobile
    height: 60px
    width: 100%
    position: relative
    overflow: hidden
    padding: 16px 0
    &__one
      margin-bottom: 16px
    &__two
      margin-top: 32px

    .product__line
      background-size: 50px
      position: absolute
      left: 0
      top: 0
      width: 100px
      height: 2000%
      transform: rotate(-90deg) translateX(899%)

  @media (max-width: $tablet-width)
    .banner__title
      min-width: auto

    .parallax__container
      justify-content: flex-end
      .product__line
        width: 80px
        margin-left: 25px
        &:first-child
          margin-left: 0

  @media (max-width: 876px)
    .banner__title
      width: 52%

  @media (max-width: 768px)
    .banner__title
      min-width: 65%

    .parallax__container
      .product__line
        &:first-child
          display: none

  @media (max-width: 736px)
    .banner__title
      min-width: 430px

  @media (max-width: $phone-width)
    .banner__container
      min-height: auto
      height: auto
      padding-bottom: 32px

    .group__product
      margin-top: 16px

    .desktop__grid
      display: none

    .mobile__grid
      display: block

</style>
