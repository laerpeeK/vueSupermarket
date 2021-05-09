<template>
  <div id="detail">
    <detail-nav-bar></detail-nav-bar>
  </div>
</template>

<script>
import DetailNavBar from "./childComponents/DetailNavBar";

import {getDetail} from "../../network/detail";

export default {
  name: "Detail",
  components: {
    DetailNavBar
    },
  data(){
    return {
      id: null
    }
  },
  created() {
    this.id = this.$route.params.id;

    getDetail(this.id).then(
        res => console.log(res)
    )
  }
}
</script>

<style scoped>

</style>