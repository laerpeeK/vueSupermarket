<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <Swiper>
      <SwiperItem v-for="item in banner">
        <a :href="item.link">
          <img :src="item.image">
        </a>
      </SwiperItem>
    </Swiper>
  </div>
</template>

<script>
import NavBar from "../../components/common/navbar/NavBar";
import {getHomeMultidata} from "../../network/home";
import {Swiper,SwiperItem} from '../../components/common/swiper';

export default {
  name: "Home",
  components:{
    NavBar,
    Swiper,
    SwiperItem
  },
  data() {
    return {
      banner:[],
      recommend: []
    }
  },
  created() {
    getHomeMultidata().then(
        res => {
          console.log(res);
          this.banner = res.data.banner.list;
          this.recommend = res.data.recommend.list;
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