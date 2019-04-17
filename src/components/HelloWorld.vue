<template>
  <canvas id="canvas"></canvas>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
//import cnn from '@/index';

@Component
export default class HelloWorld extends Vue {
  private worker;
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
      _this.worker.postMessage({
        data: _this.$el.getContext('2d').getImageData(0,0,_this.$el.width,_this.$el.height).data,
        width: _this.$el.width,
        height: _this.$el.height,
      });
/*
      cnn(
        _this.$el.getContext('2d').getImageData(0,0,_this.$el.width,_this.$el.height).data,
        _this.$el.width,
        _this.$el.height,
        _this.$el
      );
      */
    };
  }

  mounted() {
    this.worker = new Worker('./index.js');
    const _this = this;
    this.worker.onmessage = (e) => {
      const ctx = _this.$el.getContext('2d');
      ctx.putImageData(e.data, 0, 0);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
