<template>
  <div id="app">
    <input v-on:change="selectFile" type="file" id="file">
    <HelloWorld :file="file"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from './components/HelloWorld.vue';

@Component({
  components: {
    HelloWorld,
  },
  data() {
    return {
      file: '',
    }
  },
  methods: {
    selectFile(e) {
    
      var file = e.target.files;
      var reader = new FileReader();
      //ファイルが複数読み込まれた際に、1つめを選択
      reader.readAsDataURL(file[0]);
      //ファイルが読み込めたら
      let _this = this;
      reader.onload = function () {
        _this.file = reader.result;
        
      };
    }
  }
})
export default class App extends Vue {}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
