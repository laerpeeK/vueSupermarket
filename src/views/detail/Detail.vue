<template>
  <div id="detail">
    <detail-nav-bar></detail-nav-bar>
    <detail-swiper :top-images="topImages"></detail-swiper>
  </div>
</template>

<script>
import DetailNavBar from "./childComponents/DetailNavBar";
import DetailSwiper from "./childComponents/DetailSwiper";

import {getDetail} from "../../network/detail";

export default {
  name: "Detail",
  components: {
    DetailNavBar,
    DetailSwiper
    },
  data(){
    return {
      id: null,
      topImages: null,

    }
  },
  created() {
    //1.保持ID
    this.id = this.$route.params.id;

    //2.获取数据
    getDetail(this.id).then(
        res => {
          this.topImages = res.result.itemInfo.topImages

        }
    )
  }
}
</script>

<style scoped>

</style>