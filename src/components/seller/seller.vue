<template>
  <div class="seller" ref="seller">
    <div class="sller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <span class="text">{{seller.ratingCount}}</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <i class="icon-favorite" :class="{'active': favorite}"></i>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <div class="title">公告与活动</div>
        <div class="content-wrapper border-1px">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item border-1px" v-for="item in seller.supports">
            <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
            <span class="text">{{seller.supports[$index].description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {saveToLocal, LoadFromLocal} from 'common/js/store';
  import star from 'components/star/star';
  import split from 'components/split/split';

  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return LoadFromLocal(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏';
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
    },
    watch: {
      'seller'() {
        this._initScroll();
        this._initPics();
      }
    },
    methods: {
      toggleFavorite(event) {
        if (!event._constructed) {
          return;
        }
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id, 'favorite', this.favorite);
      },
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.seller, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$refs.picList.style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$refs.picWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical',
                click: true
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
      }
    },
    ready() {
      this._initScroll();
      this._initPics();
    },
    components: {
      star,
      split
    }
  };
</script>

<style lang="stylus" type="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";

  .seller
    position absolute
    top 1.74rem
    bottom 0
    width 100%
    overflow hidden
    .overview
      position relative
      padding 0.18rem
      .title
        margin-bottom  0.08rem
        line-height 0.14rem
        color rgb(7, 17, 27)
        font-size 0.14rem
      .desc
        padding-bottom 0.18rem
        border-1px(rgba(7, 17, 27, 0.1))
        font-size 0
        .star
          display inline-block
          margin-right 0.08rem
          vertical-align top
        .text
          display inline-block
          margin-right 0.12rem
          line-height 0.18rem
          vertical-align top
          font-size 0.1rem
          color rgb(77, 85, 93)
      .remark
        display flex
        padding-top 0.18rem
        .block
          flex 1
          text-align center
          border-right 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border none 
          h2
            margin-bottom 0.04rem
            line-height 0.1rem
            font-size 0.1rem
            color rgb(147, 153, 159)
          .content
            line-height 0.24rem
            font-size 0.1rem
            color rgb(7, 17, 27)
            .stress
              font-size 0.24rem
      .favorite
        position absolute
        width 0.5rem
        right 0.11rem
        top 0.18rem
        text-align center
        .icon-favorite
          display block
          margin-bottom 0.04rem
          line-height 0.24rem
          font-size 0.24rem
          color #d4d6d9
          &.active
            color rgb(240, 20, 20)
        .text
          font-size 0.12rem
          color rgb(7, 17, 27)
    .bulletin
      padding 0.18rem 0.18rem 0 0.18rem
      .title
        margin-bottom 0.08rem
        line-height 0.14rem
        color rgb(7, 17, 27)
        font-size 0.14rem
      .content-wrapper
        padding 0 0.12rem 0.16rem 0.12rem
        border-1px(rgba(7, 17, 27, 0.1))
        .content
          line-height 0.24rem
          font-size 0.12rem
          color rgb(240, 20, 20)
      .supports
        .support-item
          padding 0.16rem 0.12rem
          border-1px(rgba(7, 17, 27, 0.1))
          font-size 0
          &:last-child
            border-none()
        .icon
          display inline-block
          width .16rem
          height .16rem
          vertical-align top
          margin-right .06rem
          background-size .16rem .16rem
          background-repeat no-repeat
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
          line-height 0.16rem
          font-size 0.12rem
          color rgb(7, 17, 27)
    .pics
      padding 0.18rem
      .title
        margin-bottom 0.12rem
        line-height 0.14rem
        color rgb(7, 17, 27)
        font-size 0.14rem
      .pic-wrapper
        width 100%
        overflow hidden
        white-space nowrap
        .pic-list
          font-size 0
          .pic-item
            display inline-block
            margin-right 0.06rem
            width 1.2rem
            height 0.9rem
            &:last-child
              margin 0
            img
              width 100%
              height 100%
    .info
      padding 0.18rem 0.18rem 0 0.18rem
      .title
        padding-bottom 0.12rem
        line-height 0.14rem
        border-1px(rgba(7, 17, 27, 0.1))
        color rgb(7, 17, 27)
        font-size 0.14rem
      .info-item
        padding 0.16rem 0.12rem
        line-height 0.16rem
        border-1px(rgba(7, 17, 27, 0.1))
        font-size 0.12rem
        &:last-child
          border-none()
</style>