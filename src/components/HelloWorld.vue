<template>
  <div>
    <input v-on:change="selectFile" type="file" id="file">
    <canvas id="canvas"></canvas>
    <button v-on:click="send()">実行</button>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
//import cnn from '@/index';

@Component
export default class HelloWorld extends Vue {
  private worker;
  private file;

  send() {
    let canvas = this.$el.getElementsByTagName('canvas')[0];
    const ctx = canvas.getContext('2d');
    this.worker.postMessage({
      data: ctx.getImageData(0,0,canvas.width,canvas.height).data,
      width: canvas.width,
      height: canvas.height,
    });
  }

  selectFile(e) {
    var file = e.target.files;
    var reader = new FileReader();
    //ファイルが複数読み込まれた際に、1つめを選択
    reader.readAsDataURL(file[0]);
    //ファイルが読み込めたら
    let _this = this;
    reader.onload = function () {
      var image = new Image();
      image.src = reader.result;
      let canvas = _this.$el.getElementsByTagName('canvas')[0];
      image.onload = () => {
        canvas.width = image.width;
        canvas.height = image.height;
        const ctx = canvas.getContext('2d');
        ctx.drawImage(image, 0, 0);
      };
    };
  }
  mounted() {
    this.worker = new Worker('./index.js');
    const _this = this;
    this.worker.onmessage = (e) => {
      const ctx = _this.$el.getElementsByTagName('canvas')[0].getContext('2d');
      ctx.putImageData(e.data, 0, 0);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
