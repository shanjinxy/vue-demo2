<template lang="html">
  <div class="ratings" ref="ratingwrapper">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @only-content="chooseOnlyContent" @select-type='onSelectType' :select-type="selectType"
                    :only-content="onlyContent" :desc="desc" :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item border-1px">
            <div class="avatar">
              <img :src="rating.avatar" width="28" height="28" alt="">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span v-for="item in rating.recommend" class="item">{{item}}</span>
              </div>
              <div class="time">{{rating.rateTime | time}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
  import star from '../../components/star/star.vue'
  import split from '../../components/split/split.vue'
  import ratingselect from '../../components/ratingselect/ratingselect.vue'
  import axios from 'axios'
  import BScroll from 'better-scroll'
  import time from '../../filter/time'
  const ALL = 2
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      star,
      split,
      ratingselect
    },
    created () {
      axios.get('static/data.json').then((res) => {
        this.ratings = res.data.ratings
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.ratingwrapper, {
              click: true
            })
          } else {
            this.scroll.refresh();
          }
        })
      })
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      chooseOnlyContent () {
        this.onlyContent = !this.onlyContent
        this.$nextTick(() => {
          this.scroll.refresh();
        })
      },
      onSelectType (type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        })
      },
      needShow (type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType;
        }
      }
    }
  }

</script>
<style lang="stylus" rel="stylesheet/stylus" scoped>
  @import '../../common/stylus/mixin.styl'
  .ratings
    position fixed
    top 174px
    bottom 0
    width 100%
    left 0
    overflow hidden
    .overview
      display flex
      padding 18px 0
      .overview-left
        flex 0 0 137px
        width 137px
        border-right 1px solid rgba(7, 17, 27, 0.1)
        text-align center
        padding 6px 0
        .score
          line-height 28px
          font-size 24px
          color rgb(255, 153, 0)
          margin-bottom 6px
        .title
          font-size 12px
          color rgb(7, 17, 27)
          line-height 12px
          margin-bottom 8px
        .rank
          line-height 10px
          font-size 10px
          color rgb(147, 153, 159)
      .overview-right
        flex 1
        padding-left 24px
        .score-wrapper
          font-size 0px
          margin-bottom 8px
          line-height 18px
          .title
            display inline-block
            line-height 18px
            vertical-align top
            font-size 12px
            color: rgb(7, 17, 27)
          .star
            display inline-block
            margin 0 12px
            vertical-align top
          .score
            display inline-block
            line-height 18px
            color: rgb(255, 153, 0)
            font-size 12px
        .delivery-wrapper
          font-size 0
          .title
            line-height 18px
            font-size 12px
            color: rgb(7, 17, 27)
          .delivery
            font-size 12px
            line-height 18px
            color rgb(147, 153, 159)
            margin-left 12px

    .rating-wrapper
      padding: 0 18px
      .rating-item
        display flex
        padding 18px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          flex: 0 0 28px
          width: 28px
          margin-right 12px
          img
            border-radius 50%
        .content
          position relative
          flex 1
          .name
            margin-bottom 4px
            line-height 12px
            font-size 10px
            color: rgb(7, 17, 27)
          .star-wrapper
            margin-bottom 6px
            font-size 0px
            .star
              display inline-block
              margin-right 6px
              vertical-align top
            .delivery
              display inline-block
              vertical-align top
              font-size 10px
              color: rgb(147, 153, 159)
          .text
            font-size 12px
            line-height 18px
            margin-bottom 8px
            color: rgb(7, 17, 27)
          .recommend
            line-height 16px
            font-size 0
            .icon-thumb_up, .item
              display inline-block
              margin 0 8px 4px 0
              font-size 10px
            .icon-thumb_up
              color: rgb(0, 160, 220)
            .item
              padding 0 6px
              border: 1px solid rgba(7, 17, 27, 0.2)
              border-radius 1px
              color: rgb(147, 153, 159)
              background #fff


          .time
            position absolute
            top:0
            right 0
            font-size 10px
            font-weight 200
            line-height 12px
            color:rgb(147,153,159)
</style>
