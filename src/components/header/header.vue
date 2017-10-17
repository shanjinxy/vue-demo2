<template lang="html">
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class='brand'></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="supports">
          <span class="icon" :class="iconClassMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="support-count" v-if="seller.supports" @click="showDetail">
        <span class="count">{{seller.supports.length+'个'}}</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%" alt="">
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :score="seller.score" :size="48"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="support-item" v-for="item in seller.supports">
                <span class="icon" :class="iconClassMap[item.type]"></span>
                <span class="text">{{item.description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              {{seller.bulletin}}
            </div>
          </div>
        </div>
        <div class="detail-close">
          <i class="icon-close" @click="hideDetail()"></i>
        </div>
      </div>
    </transition>

  </div>
</template>
<script>
  import star from '../star/star.vue'
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      star: star
    },
    data() {
      return {
        detailShow: false
      }
    },
    methods: {
      showDetail() {
        this.detailShow = true
      },
      hideDetail() {
        this.detailShow = false
      }

    },
    created() {
      this.iconClassMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .header
    position relative
    background-color rgba(7, 17, 27, 0.5)
    blur: 10px
    overflow hidden
    color: #fff
    .content-wrapper
      font-size 0
      display flex
      padding: 24px 12px 18px 24px
      position relative
      .avatar
        img
          border-radius 2px
      .content
        font-size 14px
        margin-left: 16px
        .title
          margin 2px 0 8px 0
          .brand
            width 30px
            height 18px
            display inline-block
            vertical-align top
            margin-right 6px
            bg-image('brand')
            background-size 30px 18px
            background-repeat no-repeat
          .name
            font-size 16px
            font-weight bold
            line-height 18px
        .description
          font-size 12px
          margin-bottom 10px
        .supports
          display flex
          .icon
            display inline-block
            width 12px
            height: 12px
            margin-right 4px
            background-size 12px 12px
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')
          .text
            line-height 12px
            font-size 10px
            vertical-align top
      .support-count
        position absolute
        right 12px
        bottom 18px
        padding 0 8px
        height 24px
        line-height 24px
        border-radius 14px
        background-color rgba(0, 0, 0, 0.2)
        text-align center
        .count
          vertical-align top
          margin-left 2px
          font-size 10px
        .icon-keyboard_arrow_right
          font-size 10px
          margin-left 2px
          line-height 24px
    .bulletin-wrapper
      position relative
      padding: 0 22px 0 12px
      height 28px
      line-height 28px
      white-space nowrap
      overflow hidden
      text-overflow ellipsis
      .bulletin-title
        display inline-block
        width: 22px
        height 12px
        bg-image(bulletin)
        background-size 22px 12px
      .bulletin-text
        margin 0 4px
        vertical-align top
        font-size 10px
      .icon-keyboard_arrow_right
        position absolute
        font-size 10px
        right: 12px
        top: 8px

    .background
      position absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index -1
      filter: blur(10px)
    .detail
      position fixed
      z-index 100
      width 100%
      height 100%
      background-color rgba(7, 17, 27, 0.8)
      top 0
      left 0
      .detail-wrapper
        min-height 100%
        width 100%
        .detail-main
          margin-top 64px
          padding-bottom 64px
          .name
            font-size 16px
            font-weight 700
            line-height 16px
            text-align center
          .star-wrapper
            text-align center
            margin 16px 11px 28px 0
          .title
            display flex
            width 80%
            margin 0px auto  28px auto
            .line
             display inline-block
             height 1px
             flex 1
             background rgba(255,255,255,0.2)
             margin auto
            .text
              padding 0 12px
              font-size 14px
              font-weight 700

          .supports
            padding 0 0 28px 36px
            .support-item
              padding  0 6px 12px 16px
              .text
                vertical-align top
                font-size 12px
                font-weight 200
                line-height 12px
              .icon
                display inline-block
                width: 16px
                height: 16px
                background-size 16px 16px
                margin-right 6px
                background-repeat no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')
          .bulletin
            padding  0 48px
            font-size 12px
            font-weight 200px
            line-height 24px
      .detail-close
        position relative
        width: 32px
        height: 32px
        margin -64px auto 0 auto
        clear both
        font-size 32px
      &.fade-enter-active,&.fade-leave-active
        transition opacity 0.5s
      &.fade-enter,&.fade-leave-active
        opacity 0

</style>
