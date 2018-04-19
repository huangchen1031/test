<template>
<div class="viewer">
  <img v-show="!show" :src="png">
  <imageviewer 
    :images="images"
    :isShow="show"
    :button="false"
    :default-index="index"
    @hide="show = false"
  >
  </imageviewer>
  <!-- <el-button @click.native="getImages">从cookie获取</el-button> 
  <el-button @click.native="show = !show">打开神奇的照片查看器</el-button>-->
  <!-- <el-button @click.native="index = 0">去1</el-button> -->
  <!-- <el-button @click.native="images.push(images[0])">再加一次第一张</el-button> -->
</div>
</template>


<script type="text/javascript">
import imageviewer from '../../modules/imageviewer';

export default {
  name: 'viewerTest',
  components: { imageviewer },
  data() {
    return {
      show: true,
      index: 0,
      timer: null,
      images: [],
      png: `${window.location.protocol}//${window.location.host}/3.0/no-image.jpg`,
    };
  },
  methods: {
    getImages() {
      if (JSON.stringify(this.images) === this.$cookie.get('images')) {
        return;
      }
      const cookieImages = JSON.parse(this.$cookie.get('images'));
      if (cookieImages) {
        if (cookieImages.length) {
          this.images = cookieImages;
          this.show = true;
        } else {
          this.images.length = 0;
          this.show = false;
        }
      }
    },
  },
  mounted() {
    this.getImages();
    this.timer = setInterval(this.getImages, 1000);
  },
};
</script>

<style lang="scss">
.viewer{
  height: 100%;
  width: 100%;
  text-align: center;
  background-color: #e9e9eb;
}
</style>
