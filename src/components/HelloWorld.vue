<template>
  <canvas id="canvas"></canvas>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import cnn from '@/index';

@Component
export default class HelloWorld extends Vue {
  @Prop({}) private file: any
   

  @Watch('file')
  drawCanvas() {
    var image = new Image();
    image.src = this.file;
    let _this = this;
    image.onload = () => {
      _this.$el.width = image.width;
      _this.$el.height = image.height;
      _this.$el.getContext('2d').drawImage(image, 0, 0);
      cnn(
        _this.$el.getContext('2d').getImageData(0,0,_this.$el.width,_this.$el.height).data,
        _this.$el.width,
        _this.$el.height,
        _this.$el
      );
    };
  }

  mounted() {
    console.log(this.$el.getContext('2d').getImageData(0,0,1,2).data);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
