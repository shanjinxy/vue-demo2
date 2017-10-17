<template lang="html">
  <div class="cartcontrol">
    <transition name="fadeRotate">
      <div class="cart-decrease " v-show="food.count"
           @click.stop.prevent='decreaseCart($event)'>
        <span class="icon-remove_circle_outline inner"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add" @click.stop.prevent="addCart($event)">
      <i class="icon-add_circle"></i>
    </div>
  </div>
</template>
<script>
  import Vue from 'vue';
  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart (event) {
        this.count++;
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 0)
        }
        this.food.count++;
      },
      decreaseCart () {
        if (!event._constructed || !this.food.count) {
          return
        }
        this.food.count--;
      }
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    .cart-decrease
      display inline-block
      padding 6px
      transition:all .4s linear
      .inner
        line-height 24px
        font-size 24px
        color:rgb(0,160,220)
        transition :all .4s linear
      &.fadeRotate-enter-active,&.fadeRotate-leave-active
        transform translate3D(0,0,0);
        .inner
          display inline-block
          transform rotate(0)
      &.fadeRotate-enter,&.fadeRotate-leave-active
        opacity 0
        transform translate3d(24px,0px,0)
        .inner
         transform rotate(180deg)
    .cart-add
      display inline-block
      padding 6px
      font-size 24px
      line-height 24px
      color: rgb(0, 160, 220)
    .cart-count
      display inline-block
      vertical-align top
      width 12px
      padding-top 6px
      text-align center
      line-height 24px
      font-size 10px
      color: rgb(147, 153, 159)
    .cart-add
      display inline-block

</style>
