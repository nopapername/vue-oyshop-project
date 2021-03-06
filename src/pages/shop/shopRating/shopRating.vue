<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{ info.score }}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家99%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :score="info.serviceScore" :size="36" />
            <span class="score">{{ info.serviceScore }}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :score="info.foodScore" :size="36" />
            <span class="score">{{ info.foodScore }}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{ info.deliveryTime }}分钟</span>
          </div>
        </div>
      </div>

      <div class="split"></div>

      <div class="ratingselect">
        <div class="rating-type border-1px">
          <span class="block positive" @click="setSelectType(2)" :class="{active: (selectType === 2)}">
            全部<span class="count">{{ ratings.length }}</span>
          </span>
          <span class="block positive" @click="setSelectType(0)" :class="{active: (selectType === 0)}">
            满意<span class="count">{{ positiveSize }}</span>
          </span>
          <span class="block negative" @click="setSelectType(1)" :class="{active: (selectType === 1)}">
            不满意<span class="count">{{ ratings.length - positiveSize }}</span>
          </span>
        </div>
        <div class="switch" :class="{on: onlyShowText}" @click="toggleOnlyShowText">
          <span class="iconfont iconqueding"></span>
          <span class="text">只看有内容的评价</span>
        </div>
      </div>

      <div class="rating-wrapper">
        <ul>
          <li class="rating-item" v-for="(rating, index) in filterRatings" :key="index">
            <div class="avatar">
              <img :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{ rating.username }}</h1>
              <div class="star-wrapper">
                <star :score="rating.score" :size="24" />
                <span class="delivery">{{ rating.deliveryTime }}</span>
              </div>
              <p class="text">{{ rating.text }}</p>
              <div class="recommend">
                <span class="iconfont" :class="rating.rateType === 0 ? 'icontuijiansel':'icontucao-tianchong'"></span>
                <span class="item" v-for="(item, index) in rating.recommend" :key="index">{{ item }}</span>
              </div>
              <div class="time">{{ rating.rateTime }}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import {mapState, mapGetters} from 'vuex'
import star from '../../../components/stars/star'
export default {
  data () {
    return {
      onlyShowText: true, // 是否只显示有评价的
      selectType: 2 // 选择的评价类型（0满意，1不满意，2全部
    }
  },
  mounted () {
    this.$store.dispatch('getShopRatings', () => {
      this.$nextTick(() => {
        // eslint-disable-next-line
        new BScroll(this.$refs.ratings, {
          click: true
        })
      })
    })
  },
  components: {
    star
  },
  computed: {
    ...mapState(['info', 'ratings']),
    ...mapGetters(['positiveSize']),
    filterRatings () {
      const {ratings, onlyShowText, selectType} = this
      // 产生过滤后的评论数组
      return ratings.filter(rating => {
        const {rateType, text} = rating
        return (selectType === 2 || selectType === rateType) && (!onlyShowText || text.length > 0)
      })
    }
  },
  methods: {
    setSelectType (selectType) {
      this.selectType = selectType
    },
    toggleOnlyShowText () {
      this.onlyShowText = !this.onlyShowText
    }
  }
}
</script>

<style lang="stylus" scoped rel="stylesheet/stylus">
  @import "../../../common/stylus/mixins.styl"
  .ratings
    position: absolute
    top: 3.65rem
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    background: #fff
    .overview
      display: flex
      padding: 0.2rem 0
      .overview-left
        flex: 0 0 2.5rem
        padding: 0.1rem 0
        width: 2.5rem
        border-right: 0.02rem solid rgba(7, 17, 27, 0.1)
        text-align: center
        @media only screen and (max-width: 320px)
          flex: 0 0 2.2rem
          width: 2.2rem
        .score
          margin-bottom: 0.12rem
          line-height: 0.56rem
          font-size: 0.48rem
          color: rgb(255, 153, 0)
        .title
          margin-bottom: 0.16rem
          line-height: 0.24rem
          font-size: 0.24rem
          color: rgb(7, 17, 27)
        .rank
          line-height: 0.2rem
          font-size: 0.2rem
          color: rgb(147, 153, 159)
      .overview-right
        flex: 1
        padding: 0.12rem 0 0.12rem 0.15rem
        @media only screen and (max-width: 320px)
          padding-left: 0.12rem
        .score-wrapper
          margin-bottom: 0.16rem
          font-size: 0
          .title
            display: inline-block
            line-height: 0.36rem
            vertical-align: top
            font-size: 0.24rem
            color: rgb(7, 17, 27)
          .star
            display: inline-block
            margin: 0 0.24rem
            vertical-align: top
          .score
            display: inline-block
            line-height: 0.36rem
            vertical-align: top
            font-size: 0.24rem
            color: rgb(255, 153, 0)
        .delivery-wrapper
          font-size: 0
          .title
            line-height: 0.36rem
            font-size: 0.24rem
            color: rgb(7, 17, 27)
          .delivery
            margin-left: 0.24rem
            font-size: 0.24rem
            color: rgb(147, 153, 159)
    .split
      width: 100%
      height: 0.1rem
      border-top: 0.02rem solid rgba(7, 17, 27, 0.1)
      border-bottom: 0.02rem solid rgba(7, 17, 27, 0.1)
      background: #f3f5f7
    .ratingselect
      .rating-type
        padding: 0
        margin: 0 0.36rem
        border-1px(rgba(7, 17, 27, 0.1))
        font-size 0.3rem
        .block
          display: inline-block
          padding: 0.16rem 0.24rem
          margin-right: 0.16rem
          line-height: 0.32rem
          border-radius: 0.05rem
          font-size: 0.24rem
          color: rgb(77, 85, 93)
          background: rgba(77, 85, 93, 0.2)
          &.active
            background: $blue
            color: #fff
          .count
            margin-left: 0.04rem
            font-size: 0.16rem
      .switch
        padding: 0.24rem 0.36rem
        line-height: 0.48rem
        border-bottom: 0.02rem solid rgba(7, 17, 27, 0.1)
        color: rgb(147, 153, 159)
        font-size: 0
        &.on
          .iconqueding
            color: $blue
        .iconqueding
          display: inline-block
          vertical-align: top
          margin-right: 0.08rem
          font-size: 0.48rem
        .text
          display: inline-block
          vertical-align: top
          font-size: 0.24rem
    .rating-wrapper
      padding: 0 0.36rem
      .rating-item
        display: flex
        padding: 0.36rem 0
        bottom-border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          flex: 0 0 0.56rem
          width: 0.56rem
          margin-right: 0.24rem
          img
            border-radius: 50%
            width 0.56rem
            height 0.56rem
        .content
          position: relative
          flex: 1
          .name
            margin-bottom: 0.08rem
            line-height: 0.24rem
            font-size: 0.2rem
            color: rgb(7, 17, 27)
          .star-wrapper
            margin-bottom: 0.12rem
            font-size: 0
            .star
              display: inline-block
              margin-right: 0.12rem
              vertical-align: top
            .delivery
              display: inline-block
              vertical-align: top
              line-height: 0.24rem
              font-size: 0.2rem
              color: rgb(147, 153, 159)
          .text
            margin-bottom: 0.16rem
            line-height: 0.36rem
            color: rgb(7, 17, 27)
            font-size: 0.24rem
          .recommend
            line-height: 0.32rem
            font-size: 0
            .icontuijiansel, .icontucao-tianchong, .item
              display: inline-block
              margin: 0 0.16rem 0.08rem 0
              font-size: 0.18rem
            .icontuijiansel
              color: $yellow
            .icontuijiansel
              color: rgb(147, 153, 159)
            .item
              padding: 0 0.12rem
              border: 0.02rem solid rgba(7, 17, 27, 0.1)
              border-radius: 0.02rem
              color: rgb(147, 153, 159)
              background: #fff
          .time
            position: absolute
            top: 0
            right: 0
            line-height: 0.24rem
            font-size: 0.2rem
            color: rgb(147, 153, 159)
</style>
