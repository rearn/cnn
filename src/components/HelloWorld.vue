<template>
  <div>
    <input v-on:change="selectFile" type="file" id="file">
    <canvas id="canvas"></canvas>
    <div>
      <span>aTemp</span>
      <div v-for="(v, i) in aTemp" v-bind:key="i">
        <input class="temp" type="text" v-for="(w, j) in v" v-model="aTemp[i][j]" v-bind:key="j">
      </div>
    </div>
    <div>
      <span>bTemp</span>
      <div v-for="(v, i) in bTemp" v-bind:key="i">
        <input class="temp" type="text" v-for="(w, j) in v" v-model="bTemp[i][j]" v-bind:key="j">
      </div>
    </div>
    <div>
      <label>Tr : </label><input type="text" v-model="tr">
      <label>h : </label><input type="text" v-model="h">
      <label>Rk : </label><input type="text" v-model="rk">
    </div>
    <button v-on:click="send()">実行</button>
    <button v-on:click="stop()">停止</button>
    <span>実行回数: {{ count }}</span>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component
export default class HelloWorld extends Vue {
  private worker;
  private count = 0;
  private aTemp = [
    [0, 0, 0],
    [0, 1, 0],
    [0, 0, 0],
  ];
  private bTemp = [
    [-1, -1, -1],
    [-1,  8, -1],
    [-1, -1, -1],
  ];
  private tr = -0.3;
  private h = 0.1;
  private rk = 100;// 10000;

  send() {
    let canvas = this.$el.getElementsByTagName('canvas')[0];
    const ctx = canvas.getContext('2d');
    this.count = 0;
    this.worker.postMessage({
      data: ctx.getImageData(0,0,canvas.width,canvas.height).data,
      width: canvas.width,
      height: canvas.height,
      aTemp: this.aTemp,
      bTemp: this.bTemp,
      tr: this.tr,
      h: this.h,
      rk: this.rk,
    });
  }

  stop() {
    console.log(this.aTemp)
    this.worker.terminate();
    setTimeout(this.setupWorker, 1000);
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
  setupWorker() {
    this.worker = new Worker('./index.js');
  }
  mounted() {
    this.setupWorker();
    const _this = this;
    this.worker.onmessage = (e) => {
      const ctx = _this.$el.getElementsByTagName('canvas')[0].getContext('2d');
      ctx.putImageData(e.data.image, 0, 0);
      _this.count = e.data.count;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
input.temp {
  width: 2em;
}
</style>
