<template>
  <div class="tab-control">
    <div v-for="(item,index) in titles" @click="itemClick(index)" :key="item" class="tab-control-item">
      <span :class="{active: index===currentIndex}">{{item}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "TabControl",
  props: {
    titles: {
      type: Array,
      default() {
        return []
      }
    }
  },
  data() {
    return {
      currentIndex : 0
    }
  },
  methods: {
    itemClick(index) {
      this.currentIndex = index;
      this.$emit('tabClick',index);
    }
  }
}
</script>

<style scoped>
  .tab-control {
    display: flex;
    text-align: center;
    line-height:40px;
    height: 40px;
    position: sticky;
    top: 44px;
    background-color: #fff;
    z-index: 1;
  }

  .tab-control-item {
    flex:1;
  }

  .tab-control-item span {
    padding: 5px;
  }
  .active {
    color: var(--color-high-text);
    border-bottom: 2px solid var(--color-high-text);
  }
</style>