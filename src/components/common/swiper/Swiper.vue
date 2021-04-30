<template>
  <div id="hy-swiper">
    <div class="swiper">
      <slot></slot>
    </div>
    <slot name="indicator"></slot>
    <div class="indicator">
      <slot name="indicator" v-if="showIndicator && slideCount>1">
        <div v-for="(item, index) in slideCount" class="indi-item" :class="{active: index === currentIndex-1}"></div>
      </slot>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Swiper",
    props: {
      interval: {
        type: Number,
        default: 3000
      },
      animDuration: {
        type: Number,
        default: 300
      },
      moveRatio: {
        type: Number,
        default: 0.25
      },
      showIndicator: {
        type: Boolean,
        default: true
      }

    },
    data() {
      return {
        slideCount: 0,//元素个数
        totalWidth: 0,//swiper的宽度
        swiperStyle: {},//swiper样式
        currentIndex: 1,//当前index
        scrolling: false,//是否正在滚动
      }
    },
    mounted() {
      setTimeout(()=>{
        //1.操作DOM，在前后添加Slide
        this.handleDOM();

        //2.开启定时器
        this.startTimer();
      },3000)
    },
    methods: {
      /*定时器操作*/
      startTimer() {
        this.playTimer = window.setInterval(() => {
          this.currentIndex++;
          this.scrollContent(-this.currentIndex * this.totalWidth);
        },this.interval);
      },
      stopTimer() {
        window.clearInterval(this.playTimer);
      },

      /*滚动到正确位置*/
      scrollContent(currentPosition) {
        //0.设置正在滚动
        this.scrolling = true;

        //1.开始滚动动画
        this.swiperStyle.transition = 'transform' + this.animDuration+ 'ms';
        this.setTransform(currentPosition);

        //2.判断滚动位置
        this.checkPosition();

        //3.滚动完成
        this.scrolling = false;
      },

      /*校验正确位置*/
      checkPosition() {
        window.setTimeout(()=>{
          //1.校验正确位置
          this.swiperStyle.transition = '0ms';
          if(this.currentIndex >= this.slideCount +1) {
            this.currentIndex = 1;
            this.setTransform(-this.currentIndex * this.totalWidth);
          } else if(this.currentIndex <= 0) {
            this.currentIndex = this.slideCount;
            this.setTransform(-this.currentIndex * this.totalWidth);
          }

          //2.结束移动后的回调
          this.$emit('transitionEnd',this.currentIndex -1);
        },this.animDuration)
      },

      /*设置滚动的位置*/
      setTransform(position) {
        this.swiperStyle.transform = `translate3d(${position}px, 0, 0)`;
        this.swiperStyle['-webkit-transform'] = `translate3d(${position}px), 0, 0`;
        this.swiperStyle['-ms-transform'] = `translate3d(${position}px), 0, 0`;
      },

      /*操作DOM, 在DOM前后添加Slide*/
      handleDOM() {
        //1.获取要操作的元素
        let swiperEl = document.querySelector('.swiper');
        let slidesEls = swiperEl.getElementsByClassName('slide');

        //2.保存个数
        this.slideCount = slidesEls.length;

        //3.如果大于1个，那么在前后分别添加一个slide
        if(this.slideCount >1) {
          let cloneFirst = slidesEls[0].cloneNode(true);
          let cloneLast = slidesEls[this.slideCount -1].cloneNode(true);
          swiperEl.insertBefor(cloneLast, slidesEls[0]);
          swiperEl.appendChild(cloneFirst);
        }

        //4.让swiper元素，显示第一个（目前显示前面添加的最后一个元素）
        this.setTransform(-this.totalWidth);
      },
    }
  }
</script>

<style scoped>
  #hy-swiper {
    overflow: hidden;
    position: relative;
  }
  .swiper {
    display: flex;
  }

  .indicator {
    display: flex;
    justify-content: center;
    position: absolute;
    width: 100%;
    bottom: 8px;

  }
</style>