<template lang="html">
  <div>
    <div class="shopcart">
      <div class="content" >
        <div class="content-left" @click="listToggle">
          <div class="logo-wrapper">
            <div class="logo" :class="{'active':totalCount>0}">
              <i class="icon-shopping_cart"></i>
            </div>
            <div class="num" v-show="totalCount>0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'active':totalCount>0}">
            ¥ {{totalPrice}}元
          </div>
          <div class="desc">
            另需要配送费¥{{deliveryPrice}}元
          </div>
        </div>
        <div class="content-right" :class="payClass">
          {{payDesc}}
        </div>
      </div>
      <div class="ball-container">
        <div class="" v-for="ball in balls" v-show="ball.show"></div>
      </div>
      <transition name="transHeight">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="setEmpty">清空</span>
          </div>
          <div class="list-content" ref="foodlist">
            <ul>
              <li class="food" v-for="food in selectFoods">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>¥{{food.price*food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="backdrop">
      <div class="list-mask" v-show="listShow"></div>
    </transition>
  </div>

</template>
<script>
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue'
  import BScroll from 'better-scroll';
  export default {
    props: {
      selectFoods: {
        type: Array,
        default() {
          return [
            {
              price: 10,
              count: 2
            }
          ]
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 4
      }
    },
    data () {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        fold: true
//        listShow: false
      }
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return `¥${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice
          return `还差¥${diff}元起送`;
        } else {
          return '去结算';
        }
      },
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough'
        }
      },
      listShow () {
        if (!this.totalCount) {
          this.fold = true;
          return false
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.foodlist, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
    components: {
      cartcontrol
    },
    methods: {
      setEmpty () {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        })
      },
      listToggle() {
        if (!this.totalCount) {
          return;
        }

        this.fold = !this.fold;
        console.log(this.fold);
      }
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    position fixed
    left: 0
    bottom: 0
    z-index 50
    width 100%
    height 48px
    .content
      display flex
      background #141d27
      color rgba(255, 255, 255, 0.4)
      .content-left
        flex 1
        .logo-wrapper
          display inline-block
          position relative
          top: -10px
          margin 0 12px
          padding 6px
          width: 56px
          height: 56px
          box-sizing border-box
          vertical-align top
          border-radius 50%
          background #141d27
          .logo
            width: 100%
            height 100%
            border-radius 50%
            background rgb(43, 52, 60)
            text-align center
            line-height 44px
            font-size 24px
            color: #80858a
            &.active
              background rgb(0, 160, 220)
              color: white
        .num
          position absolute
          top: 0
          right: 0
          width: 24px
          height: 16px
          line-height 16px
          text-align center
          background rgb(240, 20, 20)
          border-radius 16px
          font-size 9px
          font-weight 700
          color: #fff
          box-shadow 0 4px 8px rgba(0, 0, 0, 0, 4)
        .price
          display inline-block
          vertical-align top
          font-size 16px
          margin 12px
          box-sizing border-box
          line-height 24px
          padding-right 12px
          border-right 1px solid rgba(255, 255, 255, 0.1)
          color rgba(255, 255, 255, 0.4)
          font-weight 700
          &.active
            color: white
        .desc
          display inline-block
          line-height 24px
          vertical-align top
          font-size 10px
          margin-top 12px
          font-weight 700
      .content-right
        flex: 0 0 105px
        width 105px
        font-size 12px
        font-weight 700
        background #2b343c
        text-align center
        line-height 48px
        &.not-enough
          background #2b333b
        &.enough
          background #00b43c
          color: white
    .shopcart-list
      position absolute
      top: 0
      left: 0
      width: 100%
      background white
      transform translate3d(0, -100%, 0)
      z-index -1
      &.transHeight-enter-active, &.transHeight-leave-active
        transition all 0.5s linear
      &.transHeight-enter, &.transHeight-leave-active
        transform translate3d(0, 0, 0)
      .list-header
        height: 40px
        line-height 40px
        background #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          display inline-block
          font-size 14px
          font-weight 200
          color: rgb(7, 17, 27)
          padding-left 18px
        .empty
          position absolute
          right 8px
          font-size 12px
          color: rgb(0, 160, 220)
          padding 0 10px
      .list-content
        max-height 217px
        overflow hidden
        padding 0 18px
        background #fff
        .food
          position relative
          display flex
          height 48px
          margin 0 18px
          border-bottom 1px solid rgba(7, 17, 27, 0.1)
          .name
            flex 1
            font-size 14px
            color rgb(7, 17, 27)
            line-height 48px
            font-weight 700
          .price
            font-size 14px
            font-weight 700
            color rgb(240, 20, 20)
            padding 0 12px 0 18px
            line-height 48px
          .cartcontrol-wrapper
            font-size 14px
            margin-top 6px

  .list-mask
    position fixed
    top:0
    left:0
    width 100%
    height 100%
    z-index 40
    background rgba(7,17,27,0.6)
    backdrop-filter blur(10px)
  &.backdrop-enter-avtive,&.backdrop-leave-active
    transition opacity 0.5s
  &.backdrop-enter,&backdrop-leave-active
    opacity 0
</style>
