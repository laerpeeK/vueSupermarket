<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick"/>
    <scroll class="content" ref="scroll">
      <detail-swiper :top-images="topImages" />
      <detail-base-info :goods="goods" />
      <detail-shop-info :shop="shop" />
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info :param-info="paramInfo" ref="params"/>
      <detail-comment-info ref="comment" :comment-info="commentInfo"></detail-comment-info>
      <goods-list :goods="recommends" ref="recommend"/>
    </scroll>
  </div>
</template>

<script>
import DetailNavBar from "./childComponents/DetailNavBar";
import DetailSwiper from "./childComponents/DetailSwiper";
import DetailBaseInfo from "./childComponents/DetailBaseInfo";
import DetailShopInfo from "./childComponents/DetailShopInfo";
import DetailGoodsInfo from "./childComponents/DetailGoodsInfo";
import DetailParamInfo from "./childComponents/DetailParamInfo";
import DetailCommentInfo from "./childComponents/DetailCommentInfo";

import Scroll from "../../components/common/scroll/Scroll";
import GoodsList from "../../components/content/goodsList/GoodsList";

import {getDetail, Goods, Shop, GoodsParam, getRecommend} from "../../network/detail";
import GoodsListItem from "../../components/content/goodsList/GoodsListItem";
import {itemListenerMixin} from "../../common/mixin";
import {debounce} from "../../common/util";

export default {
  name: "Detail",
  components: {
    GoodsListItem,
    DetailShopInfo,
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      Scroll,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,
      GoodsList
    },
  mixins: [itemListenerMixin],
  data(){
    return {
      id: null,
      topImages: null,
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      themeTopYs:[]
    }
  },
  created() {
    //1.保持ID
    this.id = this.$route.params.id;

    //2.获取数据
    getDetail(this.id).then(
        res => {
          const data = res.result
          this.topImages = data.itemInfo.topImages

          this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)

          this.shop = new Shop(data.shopInfo)

          this.detailInfo = data.detailInfo

          this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)

          if (data.rate.list) {
            this.commentInfo = data.rate.list[0];
          }

        }
    )
    getRecommend().then(
        res=>{
          this.recommends = res.data.list
        }
    )
    this.getThemeTopY = debounce(()=>{
      this.themeTopYs = []
      this.themeTopYs.push(0)
      this.themeTopYs.push(this.$refs.params.$el.offsetTop)
      this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
      this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)
    },100)
  },
  methods: {
    imageLoad() {
        this.getThemeTopY()
        this.$refs.scroll.refresh()
      },
    titleClick(index) {
      console.log(index)
      this.$refs.scroll.scrollTo(0,-this.themeTopYs[index],100)
    },
  },
  mounted() {
  },
  updated() {
  },
  destroyed() {
    this.$bus.$off('itemImgLoad', this.itemImgListener)
  }
}
</script>

<style scoped>
  #detail {
    position: relative;
    z-index: 9;
    background-color: #fff;
    height: 100vh;
  }

  .detail-nav {
    position: relative;
    z-index: 9;
    background-color: #fff;
  }
  .content {
    height: calc( 100% - 44px );
    overflow: hidden;
  }
</style>