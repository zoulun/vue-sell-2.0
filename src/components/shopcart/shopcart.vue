<template>
  <div>
    <div class="shop-cart">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-warpper">
          <div class="logo" :class="{'highlight': totalCount > 0}">
            <i class="icon-shopping_cart" :class="{'highlight': totalCount > 0}"></i>
          </div>
          <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalCount > 0}">&yen;{{totalPrice}}元</div>
        <div class="desc">另需配送费&yen;{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click.stop="pay">
        <div class="pay" :class="payClass">{{payDesc}}</div>
      </div>
    </div>
    <div class="ball-container">
      <div class="ball" transition="drop" v-for="ball in balls" v-show="ball.show">
        <div class="inner inner-hook"></div>
      </div>
    </div>
    <div class="shopcart-list" v-show="listShow" transition="fold">
      <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class="empty" @click="empty">清空</span>
      </div>
      <div class="list-content" ref="listContent">
        <ul>
          <li class="food" v-for="food in selectFoods">
            <span class="name">{{food.name}}</span>
            <div class="price">
              <span>&yen;{{food.price * food.count}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol @add="addFood" :food="food"></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
  <div class="list-mask" v-show="listShow" transition="fade" @click="hideList"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from 'components/cartcontrol/cartcontrol';

  export default{
    data() {
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
        dropBalls: [],
        fold: true
      };
    },
    props: {
      selectFoods: {
        type: Array,
        default() {
          return [];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
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
          return `￥${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差￥${diff}元起送`;
        } else {
          return '去结算';
        }
      },
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
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
    methods: {
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      hideList() {
        this.fold = true;
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return;
        }
        window.alert(`支付${this.totalPrice}元`);
      },
      addFood() {
        this.$emit('add', event.target);
      }
    },
    transitions: {
      drop: {
        beforeEnter(el) {
          let count = this.balls.length;
          while (count--) {
            let ball = this.balls[count];
            if (ball.show) {
              let rect = ball.el.getBoundingClientRect();
              let x = rect.left - 32;
              let y = -(window.innerHeight - rect.top - 22);
              el.style.display = '';
              el.style.webkitTransform = `translate3d(0, ${y}px, 0)`;
              el.style.transform = `translate3d(0, ${y}px, 0)`;
              let inner = el.getElementsByClassName('inner-hook')[0];
              inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`;
              inner.style.transform = `translate3d(${x}px, 0, 0)`;
            }
          }
        },
        enter(el) {
          /* eslint-disable no-unused-vars */
          let rf = el.offsetHeight;
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0, 0, 0)';
            el.style.transform = 'translate3d(0, 0, 0)';
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = 'translate3d(0, 0, 0)';
            inner.style.transform = 'translate3d(0, 0, 0)';
          });
        },
        afterEnter(el) {
          let ball = this.dropBalls.shift();
          if (ball) {
            ball.show = false;
            el.style.display = 'none';
          }
        }
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="stylus" type="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";

  .shop-cart
    position fixed
    left 0
    bottom 0
    z-index 50
    width 100%
    height 0.48rem
    .content
      display flex
      background #141d27
      font-size 0
      .content-left
        flex 1
        .logo-warpper
          display inline-block
          position relative
          top -0.1rem
          margin 0 0.12rem
          padding 0.06rem
          width 0.56rem
          height 0.56rem
          box-sizing border-box
          vertical-align top
          border-radius 50%
          background #141d27
          .logo
            width 100%
            height 100%
            border-radius 50%
            text-align center
            background #2b343c
            &.highlight
              background rgb(0, 160, 220)
            .icon-shopping_cart
              line-height 0.44rem
              font-size 0.24rem
              color #80858a
              &.highlight
                color #fff
          .num
            position absolute
            top 0
            right 0
            width 0.24rem
            height 0.16rem
            line-height 0.16rem
            text-align center
            border-radius 16px
            font-size 0.09rem
            font-weight 700
            color #ffffff
            background rgb(240, 20, 20)
            box-shadow 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display inline-block
          vertical-align top
          margin-top 0.12rem
          line-height 0.24rem
          padding-right 0.12rem
          box-sizing border-box
          border-right 1px solid rgba(255, 255, 255, 0.1)
          font-size 0.16rem
          font-weight 700
          color rgba(255, 255, 255, 0.4)
          &.highlight
            color #fff
        .desc
          display inline-block
          vertical-align top
          line-height 0.24rem
          margin 0.12rem 0 0 0.12rem
          color rgba(255, 255, 255, 0.4)
          font-size 0.1rem
      .content-right
        flex 0 0 1.05rem
        width 1.05rem
        .pay
          height 0.48rem
          line-height 0.48rem
          text-align center
          font-size 0.12rem
          font-weight 700
          color rgba(255, 255, 255, 0.4)
          background #2b333b
          &.not-enough
            background #2b333b
          &.enough
            background #00b43c
            color #fff
    .ball-container
      .ball
        position fixed
        left 0.32rem
        bottom 0.22rem
        z-index 200
        &.drop-transition
          transition all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
          .inner
            width 0.16rem
            height 0.16rem
            border-radius 50%
            background rgb(0, 160, 220)
            transition all 0.4s linear
    .shopcart-list
      position absolute
      left 0
      top 0
      z-index -1
      width 100%
      background #000
      &.fold-transition
        transition all 0.5s
        transform translate3d(0, -100%, 0)
      &.fold-enter, &.fold-leave
        transition all 0.5s
        transform translate3d(0, 0, 0)
      .list-header
        height 0.4rem
        line-height 0.4rem
        padding 0 0.18rem
        background #f3f5f7
        border-bottom 1px solid rgba(7, 17, 27 0.1)
        .title
          float left
          font-size 0.14rem
          color rgb(7, 17, 27)
        .empty
          float right
          font-size 0.12rem
          color rgb(0, 160, 220)
      .list-content
        padding 0 0.18rem
        max-height 2.17rem
        overflow hidden
        background #fff
        .food
          position relative
          padding 0.12rem 0
          border-1px(rgba(7, 17 27, 0.1))
        .name
          line-height 0.24rem
          font-size 0.14rem
          color rgb(240, 20, 20)
      .cartcontrol-wrapper
        position absolute
        right 0
        bottom 0.06rem
  .list-mask
    position fixed
    top 0
    left 0
    width 100%
    height 100%
    z-index 40
    backdrop-filter blur(10px)
    &.fade-transition
      opacity 1
      background rgba(7, 17, 27, 0.6)
      transition all 0.5s
    &.fade-enter, &.fade-leave
      opacity 0
      background rgba(7, 17, 27, 0)
</style>