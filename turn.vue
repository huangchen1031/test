<template>
  <div class="turn">
    <div class="book" :style="styleObject">
      <div class="page front-cover" style="background: #fff">
        封面
      </div>

      <div class="page"
        v-for="page of pages"
        :key="page.pageNum"
        :page="page.pageNum"
        :class="page.class"
        :style="page.style"
        @mousedown.stop="turnStart">
        <div class="wrapper" :style="page.wrapper">
          <slot :name="page.slot"></slot>
        </div>
      </div>

      <div class="page back-cover" style="background: #000">
        封底
      </div>
    </div>
    <div class="pagination">
      <button @click="toPrevPage">上一页</button>
      <button @click="toNextPage">下一页</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'turn',
  props: {
    width: {
      type: Number,
      default: 400,
    },
    height: {
      type: Number,
      default: 300,
    },
    pageSize: {
      type: Number,
      default: 4,
    },
  },
  computed: {
    pages() {
      const pages = {
        prevPrevPage: {
          slot: 'prevPrevPage',
          class: 'prev-prev-page',
          style: `width: ${this.pageWidth}px; height: ${this.pageWidth}px;`,
          wrapper: `width: ${this.width / 2}px; height: ${this.height}px;`,
          pageNum: this.currentPageNum - 1,
        },
        prevPage: {
          slot: 'prevPage',
          class: 'prev-page',
          style: `width: ${this.pageWidth}px; height: ${this.pageWidth}px;`,
          wrapper: `width: ${this.width / 2}px; height: ${this.height}px;`,
          pageNum: this.currentPageNum,
        },
        leftPage: {
          slot: 'leftPage',
          class: 'left-page',
          style: `width: ${this.pageWidth}px; height: ${this.pageWidth}px;`,
          wrapper: `width: ${this.width / 2}px; height: ${this.height}px;`,
          pageNum: this.currentPageNum + 1,
        },
        rightPage: {
          slot: 'rightPage',
          class: 'right-page',
          style: `
            width: ${this.pageWidth}px;
            height: ${this.pageWidth}px;
            transform: translate(${this.location.x}px, ${this.location.y}px) rotate(${this.location.rotate}deg);`,
          wrapper: `
            width: ${this.width / 2}px;
            height: ${this.height}px;
            transform: rotate(${-this.location.rotate}deg) translate(${-this.location.x}px, ${-this.location.y}px);
            z-index: ${this.turning ? 2 : 3};`,
          pageNum: this.currentPageNum + 2,
        },
        nextPage: {
          slot: 'nextPage',
          class: 'next-page',
          style: `
            width: ${this.pageWidth}px;
            height: ${this.pageWidth}px;
            transform: translate(${this.width / 2 + this.location.x}px, ${this.location.y}px) rotate(${this.location.rotate}deg);
            z-index: ${this.turning ? 3 : 2};
            user-select: ${this.turning ? 'none' : 'default'};`,
          wrapper: `
            width: ${this.width / 2}px;
            height: ${this.height}px;
            transform: rotate(${-this.location.rotate}deg) translate(${this.layerX - this.location.x}px, ${this.layerY - this.location.y}px) rotate(${2 * this.location.rotate - 180}deg);`,
          pageNum: this.currentPageNum + 3,
        },
        nextNextPage: {
          slot: 'nextNextPage',
          class: 'next-next-page',
          style: `width: ${this.pageWidth}px; height: ${this.pageWidth}px;`,
          wrapper: `width: ${this.width / 2}px; height: ${this.height}px;`,
          pageNum: this.currentPageNum + 4,
        },
      };
      return pages;
    },
  },
  data() {
    return {
      styleObject: {
        width: `${this.width}px`,
        height: `${this.height}px`,
      },
      pageWidth: Math.sqrt((this.width / 2) ** 2 + this.height ** 2).toFixed(0),
      currentPageSize: this.pageSize + 4,
      currentPageNum: 3,
      location: {
        x: 0,
        y: 0,
        rotate: 0,
      },
      delta: {
        x: 0,
        y: 0,
      },
      layerX: 0,
      layerY: 0,
      turning: false,
    };
  },
  mounted() {},
  methods: {
    toPrevPage() {
      this.currentPageNum -= 2;
    },
    toNextPage() {
      this.currentPageNum += 2;
      this.$emit('next-page');
    },
    turnStart(e) {
      this.coreCompute(e, 'init');
      this.turning = true;

      document.addEventListener('mousemove', this.coreCompute);
      document.addEventListener('mouseup', this.turnEnd);
    },
    coreCompute(e, status) {
      const { clientX, clientY } = e;
      let layerX;
      let layerY;
      if (status === 'init') {
        layerX = e.layerX;
        layerY = e.layerY;
        this.delta = {
          x: clientX - layerX,
          y: clientY - layerY,
        };
      } else {
        layerX = clientX - this.delta.x;
        layerY = clientY - this.delta.y;
      }
      const pointX = this.width / 2;
      const pointY = 0;

      const k = (pointY - layerY) / (pointX - layerX);
      const kPrime = -1 / k;

      const centerX = (pointX + layerX) / 2;
      const centerY = (pointY + layerY) / 2;

      const boundary = -kPrime * centerX + centerY;
      if (boundary > 0 && boundary < 300 && status !== 'end') {
        return;
      }

      const x = (centerY - kPrime * centerX - (kPrime > 0 ? 0 : this.height)) / (k - kPrime) + (kPrime > 0 ? 0 : this.pageWidth * Math.sqrt(k ** 2 / (1 + k ** 2)));
      const y = kPrime * (x - centerX) + centerY;
      const rotate = (90 - Math.atan(layerY / (pointX - layerX)) * 180 / Math.PI).toFixed(0);

      this.location = {
        x,
        y,
        rotate,
      };

      this.layerX = layerX;
      this.layerY = layerY;

      if (status === 'end' && !(parseInt(layerX) === -this.width / 2 || parseInt(layerY) === 0)) {
        e.clientX -= e.speedX;
        e.clientY -= e.speedY;
        this.coreCompute(e, 'end');
      }
    },
    turnEnd(e) {
      document.removeEventListener('mousemove', this.coreCompute);
      document.removeEventListener('mouseup', this.turnEnd);

      const el = { clientX: e.clientX, clientY: e.clientY };

      const speedX = (e.clientX - (this.delta.x - this.width / 2)) / 100;
      const speedY = (e.clientY - this.delta.y) / 100;
      el.speedX = speedX;
      el.speedY = speedY;

      this.coreCompute(el, 'end');

      this.toNextPage();

      this.location = {
        x: 0,
        y: 0,
        rotate: 0,
      };

      this.layerX = 0;
      this.layerY = 0;
    },
  },
};
</script>

<style lang="scss">
.turn {
  position: relative;

  .book {
    position: relative;
    top: 0;

    .page {
      position: absolute;
      z-index: 0;
      overflow: hidden;
      transform-origin: 0 0;

      .wrapper {
        transform-origin: 0 0;
      }
    }

    .prev-prev-page {
      left: 0;
      top: 0;
      z-index: 1;
    }

    .prev-page {
      left: 0;
      top: 0;
      z-index: 2;
    }

    .left-page {
      left: 0;
      top: 0;
      z-index: 3;
    }

    .right-page {
      left: 50%;
      top: 0;
      z-index: 3;
    }

    .next-page {
      left: 0;
      top: 0;
      z-index: 2;
    }

    .next-next-page {
      left: 50%;
      top: 0;
      z-index: 1;
    }
  }

  .pagination {
    position: relative;
    bottom: 0;
  }

}
</style>
