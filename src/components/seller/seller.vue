<template lang="html">
  <div class="seller" ref="sellerwrapper">
    <div class="seller-content">
      <div class="overview">
        <div class="title">{{sellers.name}}</div>
        <div class="desc border-1px">
          <star :size="36" :score="sellers.score"></star>
          <span class="text">({{sellers.ratingCount}})</span>
          <span class="text">月售{{sellers.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{sellers.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{sellers.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{sellers.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click.stop.prevent="toggleFavorite($event)">
          <span>
            <i class="icon-favorite" :class="{'active':favorite}"></i>
          </span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-1px">
          <p class="content">{{sellers.bulletin}}</p>
        </div>
        <ul v-if="sellers.supports" class="supports">
          <li class="support-item border-1px" v-for="item in sellers.supports">
            <span class="icon" :class="iconClassMap[item.type]"></span>
            <span class="text">{{item.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <div class="title">商家实景</div>
        <div class="pic-wrapper" ref="picsWrapper">
          <ul class="pic-list" ref='piclist'>
            <li class="pic-item" v-for="pic in sellers.pics">
              <img :src="pic" width="120" height="90" alt="">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <div class="title border-1px">商家信息</div>
        <ul>
          <li class="info-item border-1px" v-for="info in sellers.infos">
            {{info}}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
  import axios from 'axios'
  import star from '../../components/star/star.vue'
  import split from '../../components/split/split.vue'
  import BScroll from 'better-scroll'

  export default{
    created() {
      this.iconClassMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      axios.get('static/data.json').then((res) => {
        this.sellers = res.data.seller
        console.log(this.sellers)
        this.$nextTick(() => {
          this._init()
          this._initPicScroll()
        })
      })
    },
    data () {
      return {
        sellers: {},
        collectflag: false,
        favorite: false
      }
    },
    computed: {
      favoriteText () {
        return this.favorite ? '收藏' : '未收藏'
      }
    },
    components: {
      star,
      split
    },
    methods: {
      _init() {
        this.sellerwrapper = new BScroll(this.$refs.sellerwrapper, {
          click: true
        })
      },
      _initPicScroll () {
        if (this.picScroll) {
          return
        }
        let picWidth = 120;
        let margin = 6
        let width = (picWidth + margin) * this.sellers.pics.length;
        this.$refs.piclist.style.width = (width - 6) + 'px'
        this.picScroll = new BScroll(this.$refs.picsWrapper, {
          scrollX: true
        })
      },
      toggleFavorite (event) {
        if (!event._constructed) {
          return
        }
        this.favorite = !this.favorite
      }
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .seller
    position absolute
    top 174px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .overview
      padding 18px
      .title
        padding-bottom 8px
        line-height 14px
        font-size 14px
        color: rgb(7, 17, 27)
      .desc
        padding-bottom 18px
        border-1px(rgba(7, 17, 27, 0.1))
        font-size 0px
        .star
          display inline-block
          margin-right 8px
        .text
          line-height 18px
          display inline-block
          font-size 10px
          margin-right 12px
          color: rgb(77, 85, 93)
          vertical-align top
      .remark
        display flex
        padding-top 18px
        .block
          flex 1
          text-align center
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border: none
          h2
            margin-bottom 4px
            line-height 10px
            font-size 10px
            color: rgb(147, 153, 159)
          .content
            line-height 24px
            font-size 10px
            font-weight 200
            color: rgb(7, 17, 27)
            .stress
              font-size 24px

      .favorite
        position absolute
        right 18px
        top: 18px
        text-align center
        z-index 100
        .icon-favorite
          display block
          color: #d4d6d9
          line-height 24px
          font-size 24px
          &.active
            color rgb(240, 20, 20)
        .text
          line-height 10px
          font-size 10px
          color rgb(77, 25, 93)
    .bulletin
      padding 18px 12px 0 12px
      .title
        margin-bottom 8px
        color: rgb(7, 17, 27)
        line-height 14px
      .content-wrapper
        border-1px(rgba(7, 17, 27, 0.1))
        padding 0 12px 16px 12px
        .content
          font-size 12px
          font-weight 200
          color: rgb(240, 20, 20)
          line-height 24px

      .supports
        .support-item
          padding 16px 12px
          border-1px(rgba(7, 17, 27, 0.1))
          font-size 0
        .icon
          display inline-block
          width 12px
          height: 12px
          margin-right 4px
          background-size 12px 12px
          &.decrease
            bg-image('decrease_4')
          &.discount
            bg-image('discount_4')
          &.guarantee
            bg-image('guarantee_4')
          &.invoice
            bg-image('invoice_4')
          &.special
            bg-image('special_4')
        .text
          line-height 16px
          font-size 12px
          color: rgb(7, 17, 27)
    .pics
      padding 18px
      .title
        margin-bottom 12px
        line-height 14px
        font-size 14px
        color: rgb(7, 17, 27)
      .pic-wrapper
        width 100%
        overflow hidden
        white-space nowrap
      .pic-list
        font-size 0
        .pic-item
          margin-right 6px
          display inline-block
        &:last-child
          margin 0
    .info
      padding 18px 18px 0 18px
      color: rgb(7, 17, 27)
      .title
        padding-bottom 12px
        border-1px(rgba(7, 17, 27, 0.1))
        font-size 14px
        line-height 14px
      .info-item
        padding 16px 12px
        font-size 12px
        font-weight 200
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
</style>
