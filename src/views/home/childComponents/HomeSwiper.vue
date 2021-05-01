<template>
    <Swiper>
      <SwiperItem v-for="item in banner" :key="id">
        <a :href="item.link">
          <img :src="item.image" alt="">
        </a>
      </SwiperItem>
    </Swiper>
</template>

<script>
import {Swiper, SwiperItem} from '../../../components/common/swiper'

export default {
  name: "HomeSwiper",
  props: {
    banner: {
      type: Array,
      default() {
        return []
      }
    },
    id: {
      type:Number,
      default() {
        return undefined
      }
    }
  },
  components: {
    Swiper,
    SwiperItem
  }
}
</script>

<style scoped>

</style>