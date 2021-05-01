<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <home-swiper :banner="banner" :id="id"></home-swiper>
    <home-recommend-view :recommend="recommend" :id="id"></home-recommend-view>
    <home-feature-view></home-feature-view>
    <tab-control :titles="['流行','新款','精选']"></tab-control>
    <ul>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
      <li>list</li>
    </ul>
  </div>
</template>

<script>
import NavBar from "../../components/common/navbar/NavBar";
import TabControl from "../../components/content/tabControl/TabControl";

import HomeSwiper from "./childComponents/HomeSwiper";
import HomeRecommendView from './childComponents/HomeRecommendView'
import HomeFeatureView from "./childComponents/HomeFeatureView";

import {getHomeMultidata} from "../../network/home";


export default {
  name: "Home",
  components:{
    NavBar,
    HomeSwiper,
    HomeRecommendView,
    HomeFeatureView,
    TabControl
  },
  data() {
    return {
      banner:[],
      recommend: [],
      id: undefined
    }
  },
  created() {
    getHomeMultidata().then(
        res => {
          console.log(res);
          this.banner = res.data.banner.list;
          this.recommend = res.data.recommend.list;
          this.id = res.data.id;
        }
    )
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