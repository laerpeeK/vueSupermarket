<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav"/>
    <scroll class="content" ref="scroll">
      <detail-swiper :top-images="topImages" />
      <detail-base-info :goods="goods" />
      <detail-shop-info :shop="shop" />
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info :param-info="paramInfo"/>
      <detail-comment-info ref="comment" :comment-info="commentInfo"></detail-comment-info>
      <goods-list :goods="recommends"/>
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
      itemImgListener: null
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
  },
  methods: {
    imageLoad() {
        this.$refs.scroll.refresh()
      }
    },
  mounted() {
    const refresh = debounce(this.$refs.scroll.refresh,100);
    this.itemImgListener = ()=> {
      refresh()
    }
    this.$bus.$on('itemImgLoad', this.itemImgListener)
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