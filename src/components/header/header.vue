<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span>
      <span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width=100% height=100%>
    </div>
    <div v-show="detailShow" class="detail" transition="fade">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div>
            <div>
              <div class="title">
                <div class="line"></div>
                <div class="text">优惠信息</div>
                <div class="line"></div>
              </div>
              <ul v-if="seller.supports" class="supports">
                <li class="support-item" v-for="(item, index) in seller.supports" key="index">
                  <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                  <span class="text">{{seller.supports[index].description}}</span>
                </li>
              </ul>
            </div>
            <div>
              <div class="title">
                <div class="line"></div>
                <div class="text">商家公告</div>
                <div class="line"></div>
              </div>
              <div class="bulletin">
                <p class="content">{{seller.bulletin}}</p>
              </div>
            </div>
          </div>
          
        </div>
      </div>
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from 'components/star/star';

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        detailShow: false
      };
    },
    created() {
      this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
    },
    methods: {
      showDetail() {
        this.detailShow = true;
      },
      hideDetail() {
        this.detailShow = false;
      }
    },
    components: {
      star
    }
  };
</script>

<style lang="stylus" type="stylesheet/stylus">
@import "../../common/stylus/mixin.styl";

.header
  position relative
  overflow hidden
  color: #ffffff
  background: rgba(7, 17, 27, .5)!important
  .content-wrapper
    position relative
    padding: .24rem .12rem .18rem .24rem
    font-size: 0
    .avatar
      display inline-block
      vertical-align: top
      img 
        border-radius: 2px
    .content
      display inline-block
      margin-left: .14rem
      font-size: .14rem
      .title
        margin: .02rem 0 .08rem
        .brand
          display: inline-block
          vertical-align: top
          width: .3rem
          height: .18rem
          bg-image('brand')
          background-size: .3rem .18rem
          backgound-repeat: no-repeat
        .name
          margin-left: .06rem
          font-size: .16rem
          line-height: .18rem
          font-weight: bold
      .description
        margin-bottom: .1rem
        line-height: .12rem
        font-size: .1rem
      .support
        .icon
          display inline-block
          width: .12rem
          height: .12rem
          margin-right: .04rem
          background-size: .12rem .12rem
          background-repeat: no-repeat
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
          line-height: .12rem
          font-size: .1rem
          vertical-align: middle
    .support-count
      position: absolute
      right: .12rem
      bottom: .14rem
      padding: 0 .08rem
      height: .24rem
      line-height: .24rem
      border-radius: 14px
      background: rgba(0, 0, 0, .2)
      text-align: center
      .count
        font-size: .1rem
      .icon-keyboard_arrow_right
        // margin-left: .02rem
        // line-height: .24rem
        font-size: .1rem
  .bulletin-wrapper
    position relative
    height: .28rem
    line-height: .28rem
    padding: 0 .22rem 0 .12rem
    white-space: nowrap
    overflow: hidden
    text-overflow: ellipsis
    background: rgba(7, 17, 27, .2)
    .bulletin-title
      display: inline-block
      vertical-align: top
      width: .22rem
      height: .12rem
      margin-top: .08rem
      bg-image('bulletin')
      background-size: .22rem .12rem
      background-repeat: no-repeat
    .bulletin-text
      vertical-align: top
      font-size: .1rem
      margin: 0 .04rem
    .icon-keyboard_arrow_right
      position: absolute
      font-size: .1rem
      right: .12rem
      top: .08rem
  .background
    position: absolute
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: -1
    filter: blur(10px)
  .detail
    position: fixed
    z-index: 100
    top: 0
    left: 0
    width: 100%
    height: 100%
    overflow: auto
    backdrop-filter blur(2px)
    transition all .5s
    &.fade-transition
      opacity 1
      background: rgba(7, 17, 27, .8)
    &.fade-enter, &.fade-leave
      opacity 0
      background: rgba(7, 17, 27, 0)
    .detail-wrapper
      width: 100%
      min-height: 100%
      box-sizing: border-box;
      padding 0 .36rem 0
      .detail-main
        margin-top: .64rem
        padding-bottom: .64rem
        .name
          line-height: .16rem
          text-align: center
          font-size: .16rem
          font-weight: 700
        .star-wrapper
          margin-top .18rem
          padding .02rem 0
          text-align center
        .title
          display flex
          margin .28rem auto .24rem
          .line
            flex  1
            position relative
            top -.06rem
            border-bottom 1px solid rgba(255, 255, 255, .2)
          .text
            padding 0 .12rem
            font-weight 700
        .supports
          .support-item
            padding 0 .12rem
            margin-bottom .12rem
            font-size 0
            &:last-child
              margin-bottom 0
            .icon
              display inline-block
              width .16rem
              height .16rem
              vertical-align top
              margin-right .06rem
              background-size .16rem .16rem
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
            .text
              line-height .16rem
              font-size .12rem
        .bulletin
          .content
            padding 0 .12rem
            line-height .24rem
            font-size .12rem

    .detail-close
      position relative
      width: .32rem
      height: .32rem
      margin: -.64rem auto 0 auto
      clear: both
      font-size: .32rem
.avatar
    img
      width: 0.64rem
      height: 0.64rem
</style>
