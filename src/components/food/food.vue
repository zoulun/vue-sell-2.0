<template>
  <div v-show="showFlag" class="food" transition="move" ref="food">
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image">
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="now">&yen;{{food.price}}</span>
          <span class="old" v-show="food.oldPrice">&yen;{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
        <cartcontrol @add="addFood" :food="food"></cartcontrol>
      </div>
      <div @click.stop.prevent="addFirst" class="buy" 
      v-show="!food.count || food.count === 0" transition="fade">加入购物车</div>
      </div>
      <split></split>
      <div class="info" v-show="food.info">
        <h1 class="title">商品信息</h1>
        <p class="text">{{food.info}}</p>
      </div>
      <split></split>
      <div class="rating">
        <h1 class="title">商品评价</h1>
        <ratingselect
        @select="ratingtypeSelect"
        @toggle="contentToggle"
        :ratings="food.ratings"
        :select-type="selectType" 
        :only-content="onlyContent"
        :desc="desc">
        </ratingselect>
        <div class="rating-wrapper">
          <ul v-show="food.ratings && food.ratings.length">
            <li v-show="needShow(rating.rateType, rating.text)" v-for="rating in food.ratings" class="rating-item boxder-1px">
              <div class="user">
                <span class="name">{{rating.username}}</span>
                <img class="avatar" :src="rating.avatar">
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
              <p class="text">
                <i
                :class="{'icon-thumb_up': rating.rateType === 0,
                'icon-thumb_down': rating.rateType === 1,}">
                </i>{{rating.text}}

              </p>
            </li>
          </ul>
          <div class="no-rating" v-show="!food.ratings || !food.ratings.length">
            暂无评价
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import {formatDate} from 'common/js/date';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import split from 'components/split/split';
  import ratingselect from 'components/ratingselect/ratingselect';

  // const POSITIVE = 0;
  // const NEGATIVE = 1;
  const ALL = 2;

  export default{
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = false;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        if (!event._constructed) {
          return;
        }
        this.$emit('add', event.target);
        Vue.set(this.food, 'count', 1);
      },
      addFood(target) {
        this.$emit('add', target);
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },
      ratingtypeSelect(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      contentToggle(onlyContent) {
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  };
</script>

<style lang="stylus" type="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";

  .food
    position fixed
    left 0
    top 0
    bottom 0.48rem
    z-index 30
    width 100%
    background #fff
    &.move-transition
      transition all 0.2s linear
      transform translate3d(0, 0, 0)
    &.move-enter, &.move-leave
      transform translate3d(100%, 0, 0)
    .image-header
      position relative
      width 100%
      height 0
      padding-top: 100%
      img
        position absolute
        top 0
        left 0
        width 100%
        height 100%
      .back
        position absolute
        top 0.1rem
        left 0
        .icon-arrow_lift
          display block
          padding 0.1rem
          font-size 0.2rem
          color #fff
    .content
      position relative
      padding 0.18rem
      .title
        line-height 0.14rem
        margin-bottom 0.08rem
        font-size 0.14rem
        font-weight 700
        color rgb(7, 17, 27)
      .detail
        margin-bottom 0.18rem
        line-height 0.1rem
        height 0.1rem
        font-size 0
        .sell-count, .rating
          font-size 0.1rem
          color rgb(147, 153, 159)
        .sell-count
          margin-right 0.12rem
      .price
            font-weight 700
            line-height 0.24rem
            .now
              margin-right 0.08rem
              font-size 0.14rem
              color rgb(240, 20, 20)
            .old
              text-decoration line-through
              font-size 0.10rem
              color rgb(147, 153, 159)
      .cartcontrol-wrapper
        position absolute
        right 0.12rem
        bottom 0.12rem
      .buy
        position absolute
        right 0.18rem
        bottom 0.18rem
        z-index 10
        height 0.24rem
        line-height 0.24rem
        padding 0 0.12rem
        box-sizing border-box
        border-radius 12px
        font-size 0.10rem
        color #ffffff
        background rgb(0, 160, 220)
        &.fade-transition
          opacity 1
          transition all 0.2s
        &.fade-enter, &.fade-leave
          opacity 0
    .info
      padding 0.18rem
      .title
        line-height 0.14rem
        margin-bottom 0.06rem
        font-size 0.14rem
        color rgb(7, 17, 27)
      .text
        padding 0 0.08rem
        line-height 0.24rem
        font-size 0.12rem
        color rgb(77, 85, 93)
    .rating
      padding-top 0.18rem
      .title
        line-height 0.14rem
        margin-left 0.18rem
        font-size 0.14rem
        color rgb(7, 17, 27)
      .rating-wrapper
        padding 0 0.18rem
        .rating-item
          position relative
          padding 0.16rem 0
          border-1px(rgba(7, 17, 27, 0.1))
          .user
            position absolute
            right 0
            top 0.16rem
            line-height 0.12rem
            font-size 0
            .name
              display inline-block
              margin-right 0.06rem
              vertical-align top
              font-size 0.1rem
              color rgb(147, 153, 159)
            .avatar
              width 0.12rem
              height 0.12rem
              border-radius 50%
          .time
            margin-bottom 0.06rem
            line-height 0.12rem
            font-size 0.1rem
            color rgb(147, 153, 159)
          .text
            line-height 0.16rem
            font-size 0.12rem
            color rgb(7, 17, 27)
            .icon-thumb_up, .icon-thumb_down
              margin-right 0.04rem
              line-height 0.16rem
              font-size 0.12rem
            .icon-thumb_up
              color rgb(0, 160, 220)
            .icon-thumb_down
              color rgb(147, 153, 159)
        .no-rating
          padding 0.16rem 0
          font-size 0.12rem
          color rgb(147, 153, 159)
</style>