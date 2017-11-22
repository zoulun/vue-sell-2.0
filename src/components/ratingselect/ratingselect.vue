<template>
  <div class="rating-select">
    <div class="rating-type border-1px">
      <span @click="select(2, $event)" class="block positive" :class="{'active': selectType === 2}">
        {{desc.all}}
        <span class="count">{{ratings.length}}</span>
      </span>
      <span @click="select(1, $event)" class="block positive" :class="{'active': selectType === 1}">
        {{desc.negative}}
        <span class="count">{{positives.length}}</span>
      </span>
      <span @click="select(0, $event)" class="block negative" :class="{'active': selectType === 0}">
        {{desc.positive}}
        <span class="count">{{negatives.length}}</span>
      </span>
    </div>
    <div @click="toggleContent" class="switch" :class="{'on': onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只要有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;

  export default{
    props: {
      ratings: {
        type: Array,
        default() {
          return [];
        }
      },
      selectType: {
        type: Number,
        default() {
          return ALL;
        }
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc: {
        type: Object,
        default() {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          };
        }
      }
    },
    computed: {
      positives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE;
        });
      },
      negatives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
      }
    },
    methods: {
      select(type, event) {
        if (!event._constructed) {
          return;
        }
        // this.selectType = type;
        this.$emit('select', type);
      },
      toggleContent(event) {
        if (!event._constructed) {
          return;
        }
        // this.onlyContent = !this.onlyContent;
        this.$emit('toggle', this.onlyContent);
      }
    }
  };
</script>

<style lang="stylus" type="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";

  .rating-select
    .rating-type
      padding 0.18rem 0
      margin 0 0.18rem
      border-1px(rgba(7, 17, 27, 0.1))
      font-size 0
      .block
        display inline-block
        padding 0.08rem 0.12rem
        margin-right 0.08rem
        line-height 0.16rem
        border-radius 1px
        font-size 0.12rem
        color rgb(77, 85, 93)
        &.active
          color #fff
        .count
          margin-left 0.02rem
          font-size 0.08rem
        &.positive
          background rgba(0, 160, 220, 0.2)
          &.active
            background rgb(0, 160, 220)
        &.negative
          background rgba(77, 85, 93, 0.2)
          &.active
            background rgb(77, 85, 93)
    .switch
      padding 0.12rem 0.18rem
      line-height 0.24rem
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      color rgb(147, 153, 159)
      font-size 0
      &.on
        .icon-check_circle
          color #00c850
      .icon-check_circle
        display inline-block
        vertical-align top
        margin-right 0.04rem
        font-size 0.24rem
      .text
        display inline-block
        vertical-align top
        font-size 0.12rem
</style>