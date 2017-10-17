<template lang="html">
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item border-1px" @click="menuClick(index,$event)"
            :class="index==menuCurrentIndex?'menu-item-selected':'menu-item'">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="iconClassMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="goDetail(food)" v-for='food in item.foods' class="food-item border-1px">
              <div class="icon">
                <img :src="food.icon" width="57" height="57">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">¥{{food.price}}</span><span v-show="food.oldprice" class="old">{{food.oldprice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :delivery-price="seller.deliverPrice" :min-price="seller.minPrice"
              :select-foods="selectFoods"></shopcart>
    <foodDetail :food="selectedFood" ref="myFood" v-if="selectedFood"></foodDetail>
  </div>
</template>
<script>
  import axios from 'axios';
  import BScroll from 'better-scroll';
  import shopcart from '../shopcart/shopcart.vue'
  import cartcontrol from 'components/cartcontrol/cartcontrol.vue'
  import foodDetail from '../../components/foodDetail/foodDetail.vue'
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      shopcart,
      cartcontrol,
      foodDetail
    },
    created() {
      this.iconClassMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      axios.get('static/data.json').then((res) => {
        this.goods = res.data.goods;
        this.$nextTick(() => {
          this._initScroll(); // 初始化scroll
          this._calculateHeight(); // 初始化列表高度列表
        })
      })
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        foodsScrollY: 0,
        selectedFood: {}
      }
    },
    computed: {
      menuCurrentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let topHeight = this.listHeight[i];
          let bottomHeight = this.listHeight[i + 1];
          if (!bottomHeight || (this.foodsScrollY >= topHeight) && this.foodsScrollY < bottomHeight) {
            return i
          }
        }
        return 0
      },
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        return foods
      }
    },

    methods: {
      selectMenu(index, event) {
        if (!event._constructed) {
          return;
        }
        let foodList = this.$refs.foodsWrapper.querySelectorAll('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      // 点击显示 food详情
      goDetail(food) {
        this.selectedFood = food
        this.$nextTick(() => {
          this.$refs.myFood.showToggle()
        })
      },
      _initScroll() {
        this.menuWrapper = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        });

        // 监听滚动事件
        this.foodsScroll.on('scroll', (pos) => {
          this.foodsScrollY = Math.abs(Math.round(pos.y))
//          console.log(this.foodsScrollY)
        })
      },
      _calculateHeight() {
        let foodList = this.$refs.foodsWrapper.querySelectorAll('.food-list-hook')
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      },
      menuClick(index, event) {
        if (!event._constructed) {
          return
        }
        this.foodsScroll.scrollTo(0, -this.listHeight[index], 300)
      }
    }

  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .goods
    display flex
    position absolute
    top: 174px
    bottom: 46px
    width 100%
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background: #f3f5f7
      .menu-item-selected
        background white
        font-weight 700
        margin-top -1px
      .menu-item, .menu-item-selected
        position relative
        display table
        height 54px
        width: 56px
        line-height 14px
        padding 0 12px
        &:last-child
          content: none
        .icon
          display inline-block
          width 12px
          height: 12px
          margin-right 1px
          background-size 12px 12px
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          font-size 12px
          display table-cell
          vertical-align middle
          width 56px
          border-1px(rgba(7, 17, 27, 0.1))
    .foods-wrapper
      flex 1
      .title
        padding-left 14px
        height 26px
        line-height 26px
        boder-left: 2px solid #d9dde1
        font-size 12px
        color: rgb(147, 153, 159)
        background #f3f5f7
      .food-item
        display flex
        margin 0 18px
        padding 18px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .icon
          flex: 0 0 57px
          margin-right 10px
          &:last-child
            border-bottom: none
        .content
          flex: 1
          .name
            margin 2px 0 8px 0
            height: 14px
            line-height 14px
            font-size 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            font-size 10px
            line-height 1.2
            color: rgb(147, 153, 159)
          .desc
            margin-bottom: 8px
          .extra
            .count
              margin-right: 12px
          .price
            font-weight 700
            line-height 24px
            .now
              color: rgb(240, 20, 20)
              margin-right: 8px
            .old
              font-size 10px
              color: rgb(147, 53, 159)
              text-decoration: line-through
          .cartcontrol-wrapper
            position absolute
            right 0
            bottom 12px
            z-index 20
</style>
