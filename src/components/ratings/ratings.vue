<template>
  <div class="ratings" ref="ratings">
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
            <span class="title">商品平分</span>
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
      <ratingselect
        @select="ratingtypeSelect"
        @toggle="contentToggle"
        :ratings="ratings"
        :select-type="selectType" 
        :only-content="onlyContent"
        :desc="desc">
      </ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-show="needShow(rating.rateType, rating.text)" v-for="rating in ratings" class="rating-item">
            <div class="avatar">
              <img :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span class="item" v-for="item in rating.recommend">{{item}}</span>
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import star from 'components/star/star';
  import ratingselect from 'components/ratingselect/ratingselect';
  import split from 'components/split/split';
  import {formatDate} from 'common/js/date';

  const ALL = 2;

  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: false
      };
    },
    methods: {
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
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    created() {
      this.$http.get('/api/ratings').then((res) => {
        res = res.body;
        if (res.errNo) {
          this.ratings = res.data;
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.ratings, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
      });
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    // events: {
    //   'ratingtype.select'(type) {
    //     this.selectType = type;
    //     this.$nextTick(() => {
    //       this.scroll.refresh();
    //     });
    //   },
    //   'content.toggle'(onlyContent) {
    //     this.onlyContent = onlyContent;
    //     this.$nextTick(() => {
    //       this.scroll.refresh();
    //     });
    //   }
    // },
    components: {
      star,
      ratingselect,
      split
    }
  };
</script>

<style lang="stylus" type="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";

  .ratings
    position absolute
    top 1.74rem
    bottom 0
    width 100%
    overflow hidden
    .overview
      display flex
      padding 0.18rem 0
      .overview-left
        flex 0 0 1.37rem
        width 1.37rem
        border-right 1px solid rgba(7, 17, 27, 0.1)
        text-align center
        padding 0.06rem 0
        @media only screen and (max-width: 320px)
          flex 0 0 1.2rem
          width 1.2rem
        .score
          margin-bottom 0.06rem
          line-height 0.28rem
          font-size 0.24rem
          color rgb(255, 153, 0)
        .title
          margin-bottom 0.08rem
          line-height 0.12rem
          font-size 0.12rem
          color rgb(7, 17, 27)
        .rank
          line-height 0.1rem
          font-size 0.1rem
          color rgb(147, 153, 159)
      .overview-right
        flex 1
        padding 0.06rem 0 0.06rem 0.24rem
        @media only screen and (max-width: 320px)
          padding-left 0.06rem
        .score-wrapper
          margin-bottom 0.08rem
          font-size 0
          .title
            display inline-block
            line-height 0.18rem
            font-size 0.12rem
            color rgb(7, 17, 27)
          .star
            display inline-block
            margin 0 0.12rem
            vertical-align top
          .score
            display inline-block
            line-height 0.18rem
            vertical-align top
            font-size 0.12rem
            color rgb(255, 153, 0)
        .delivery-wrapper
          font-size 0
          .title
            display inline-block
            line-height 0.18rem
            font-size 0.12rem
            color rgb(7, 17, 27)
          .delivery
            margin-left 0.12rem
            font-size 0.12rem
            color rgb(147, 153, 159)
    .rating-wrapper
      padding 0 0.18rem
      .rating-item
        display flex
        padding 0.18rem 0
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          flex 0 0 0.28rem
          width 0.28rem
          margin-right 0.12rem
          img
            width 0.28rem
            height 0.28rem
            border-radius 50%
        .content
          position relative
          flex 1
          .name
            margin-bottom 0.04rem
            line-height 0.12rem
            font-size 0.1rem
            color rgb(7, 17, 27)
          .star-wrapper
            margin-bottom 0.06rem
            font-size 0
            .star
              display inline-block
              margin-right 0.06rem
              vertical-align top
            .delivery
              display inline-block
              vertical-align top
              line-height 0.12rem
              font-size 0.1rem
              color rgb(147, 153, 159)
          .text
            margin-bottom 0.08rem
            line-height 0.18rem
            color rgb(7, 17, 27)
            font-size 0.12rem
          .recommend
            line-height 0.16rem
            font-size 0
            .icon-thumb_up, .item
              display inline-block
              margin 0 0.08rem 0.04rem 0
              font-size 0.09rem
            .icon-thumb_up
              color rgb(0, 160, 220)
            .item
              padding 0 0.06rem
              border 1px solid rgba(7, 17, 27, 0.1)
              border-radius 1px
              color rgb(147, 153, 159)
              background #fff
          .time
            position absolute
            top 0
            right 0
            line-height 0.12rem
            font-size 0.1rem
            color rgb(147, 153, 159)
</style>