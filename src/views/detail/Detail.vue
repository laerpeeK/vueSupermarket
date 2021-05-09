<template>
  <div>详情页{{id}}</div>
</template>

<script>
export default {
  name: "Detail",
  data(){
    return {
      id: null
    }
  },
  created() {
    this.iid = this.$route.params.id;
  }
}
</script>

<style scoped>

</style>