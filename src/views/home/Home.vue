<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <tab-control
        :titles="['流行','新款','精选']"
        @tabClick="tabClick"
        ref="tabControl1"
        v-show="isTabFixed"
    ></tab-control>
    <scroll class="content"
            ref="scroll"
            :probe-type="3"
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore"
    >
      <home-swiper :banner="banner" :id="id" @swiperImgLoad="swiperImgLoad"></home-swiper>
      <home-recommend-view :recommend="recommend" :id="id"></home-recommend-view>
      <home-feature-view></home-feature-view>
      <tab-control
          :titles="['流行','新款','精选']"
          @tabClick="tabClick"
          ref="tabControl2"
      ></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isTabTopShow"/>
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";
import Scroll from "components/common/scroll/Scroll";

import TabControl from "components/content/tabControl/TabControl";
import GoodsList from "components/content/goodsList/GoodsList";
import BackTop from "components/content/backTop/BackTop";

import HomeSwiper from "./childComponents/HomeSwiper";
import HomeRecommendView from './childComponents/HomeRecommendView'
import HomeFeatureView from "./childComponents/HomeFeatureView";

import {
    getHomeMultidata,
    getHomeGoods
} from "../../network/home";

import {debounce} from "common/util";
import {itemListenerMixin} from "../../common/mixin";

export default {
  name: "Home",
  components:{
    NavBar,
    Scroll,
    HomeSwiper,
    HomeRecommendView,
    HomeFeatureView,
    TabControl,
    GoodsList,
    BackTop
  },
  mixins: [itemListenerMixin],
  data() {
    return {
      banner:[],
      recommend: [],
      id: undefined,
      goods: {
        'pop': {page:0, list:[]},
        'new': {page:0, list:[]},
        'sell': {page:0, list:[]},
      },
      currentType: 'pop',
      isTabTopShow: false,
      tabOffsetTop: 0,
      isTabFixed: false,
      saveY: 0,
    }
  },
  created() {
    //请求多个数据
    this.getHomeMultidata()

    //请求商品数据
    this.getHomeGoods('pop')
    this.getHomeGoods('new')
    this.getHomeGoods('sell')

  },
  mounted() {
    //1.图片加载完成的事件监听


    //2.获取tabControl的offsetTop
    //this.tabOffsetTop = this.$refs.tabControl.$el.offsetTop;
  },
  activated() {
    this.$refs.scroll.scrollTo(0,this.saveY,0)
    this.$refs.scroll.refresh()
  },
  deactivated() {
    this.saveY = this.$refs.scroll.getScrollY()
    this.$bus.$off('itemImgLoad',this.itemImgListener)
  },
  computed: {
    showGoods() {
      return this.goods[this.currentType].list;
    }
  },
  methods: {
    //事件监听相关

    tabClick(index) {
      switch(index) {
        case 0:
          this.currentType = 'pop'
          break
        case 1:
          this.currentType = 'new'
          break
        case 2:
          this.currentType = 'sell'
          break
        default:
          this.currentType = 'pop'
      }
      this.$refs.tabControl1.currentIndex = index;
      this.$refs.tabControl2.currentIndex = index;
    },

    backClick() {
      this.$refs.scroll.scrollTo(0,0,500);
    },

    contentScroll(position) {
      this.isTabTopShow = Math.abs(position.y) > 1000? true: false;

      this.isTabFixed = Math.abs(position.y) > this.tabOffsetTop? true: false
    },

    swiperImgLoad() {
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop
    },

    //网络请求相关
    getHomeMultidata() {
      getHomeMultidata().then(
          res => {
            this.banner = res.data.banner.list;
            this.recommend = res.data.recommend.list;
            this.id = res.data.id;
          }
      )
    },

    getHomeGoods(type) {
      const page = this.goods[type].page+1;
      getHomeGoods(type,page).then(
          res => {
            this.goods[type].list.push(...res.data.list);
            this.goods[type].page++;
          }
      )
    },

    loadMore() {
      this.getHomeGoods(this.currentType)
      this.$refs.scroll.finishPullUp();
    }
  }
}
</script>

<style scoped>
  #home {
    /*padding-top: 44px;*/
    height: 100vh;
    position: relative;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
  }

  .content {
    overflow: hidden;
    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }

</style>