<template>
  <div class="cartcontrol">
    <div class="cart-decrease" 
    @click.stop.prevent="decreaseCart" v-show="food.count > 0" transition="move">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
    <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';

  export default{
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$emit('add', event.target);
      },
      decreaseCart(event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count > 0) {
          this.food.count--;
        }
      }
    }
  };
</script>

<style lang="stylus" type="stylesheet/stylus">
  .cartcontrol
    font-size 0
    .cart-decrease
      display inline-block
      padding 0.06rem
      transition all 0.4s linear
      &.move-transition
        opacity 1
        transform translate3d(0, 0, 0)
        .inner
          display inline-block
          line-height 0.24rem
          font-size 0.24rem
          color rgb(0, 160, 220)
          transition all 0.4s linear
          transform rotate(0)
      &.move-enter, &.move-leave
        opacity 0
        transform translate3d(0.24rem, 0, 0)
        .inner
          transform rotate(180deg)
    .cart-count
      display inline-block
      vertical-align top
      width 0.24rem
      padding-top 0.06rem
      line-height 0.24rem
      text-align center
      font-size 0.1rem
      color rgb(147, 153, 159)
    .cart-add
      display inline-block
      padding 0.06rem
      line-height 0.24rem
      font-size 0.24rem
      color rgb(0, 160, 220)
</style>