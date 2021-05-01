<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <home-swiper :banner="banner" :id="id"></home-swiper>
    <home-recommend-view :recommend="recommend" :id="id"></home-recommend-view>
    <home-feature-view></home-feature-view>
    <tab-control :titles="['流行','新款','精选']" @tabClick="tabClick"></tab-control>
    <goods-list :goods="showGoods"></goods-list>
  </div>
</template>

<script>
import NavBar from "../../components/common/navbar/NavBar";

import TabControl from "../../components/content/tabControl/TabControl";
import GoodsList from "../../components/content/goodsList/GoodsList";


import HomeSwiper from "./childComponents/HomeSwiper";
import HomeRecommendView from './childComponents/HomeRecommendView'
import HomeFeatureView from "./childComponents/HomeFeatureView";

import {
    getHomeMultidata,
    getHomeGoods
} from "../../network/home";


export default {
  name: "Home",
  components:{
    NavBar,
    HomeSwiper,
    HomeRecommendView,
    HomeFeatureView,
    TabControl,
    GoodsList
  },
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
      currentType: 'pop'
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

  }
}
</script>

<style scoped>
  #home {
    padding-top: 44px;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;

    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 1;
  }
</style>