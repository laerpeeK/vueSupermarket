<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <home-swiper :banner="banner" :id="id"></home-swiper>
    <home-recommend-view :recommend="recommend" :id="id"></home-recommend-view>
  </div>
</template>

<script>
import NavBar from "../../components/common/navbar/NavBar";
import HomeSwiper from "./childComponents/HomeSwiper";
import HomeRecommendView from './childComponents/HomeRecommendView'
import HomeFeatureView from "./childComponents/HomeFeatureView";

import {getHomeMultidata} from "../../network/home";


export default {
  name: "Home",
  components:{
    NavBar,
    HomeSwiper,
    HomeRecommendView
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
  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
  }
</style>