<template>
  <div class="book-test">
    <turn @next-page="toNextPage">
      <div slot="prevPrevPage" style="background: #f00">
        封二
      </div>
      <div slot="prevPage" style="background: #0f0">
        {{currentPage - 2}}
      </div>
      <div slot="leftPage" style="background: #00f">
        {{currentPage - 1}}
      </div>
      <div slot="rightPage" style="background: #ff0">
        {{currentPage}}
      </div>
      <div slot="nextPage" style="background: #f0f">
        {{currentPage + 1}}
      </div>
      <div slot="nextNextPage" style="background: #0ff">
        封三
      </div>
    </turn>
  </div>
</template>

<script>
import turn from './turn';

export default {
  name: 'book',
  components: { turn },
  data() {
    return {
      currentPage: 3,
    };
  },
  methods: {
    toNextPage() {
      this.currentPage += 2;
    },
  },
};
</script>

<style lang="scss">
.book-test {
  position: relative;
  top: 100px;
  left: 100px;
  div {
    height: 100%;
  }
}
</style>
