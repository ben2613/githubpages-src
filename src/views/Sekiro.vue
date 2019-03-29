<template>
  <div class="container">
    <h1>YOU DIED, again and again</h1>
    <b-container>
      <b-row>
        <b-col cols="9">
          <div id="canvas-container">
            <canvas id="canvas" width="1920px" height="1080px"></canvas>
          </div>
        </b-col>
        <b-col>
          <b-row align-h="start">
            <b-col>
              <input type="text" v-model="kanji">
            </b-col>
          </b-row>
          <b-row align-h="start">
            <b-col>
              <input type="text" v-model="english">
            </b-col>
          </b-row>
          <b-row align-h="start">
            <b-col>
              <input id="transparentChk" type="checkbox" v-model="transparent">
              <label for="transparentChk"> transparent </label>
              <input id="use720pChk" type="checkbox" v-model="use720p">
              <label for="use720pChk"> Use 720P </label>
            </b-col>
          </b-row>
          <b-row align-h="start">
            <b-col>
              <button @click="downloadCanvas">Download</button>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>
<style>
@import url(https://fonts.googleapis.com/earlyaccess/cwtexkai.css);
</style>
<style scoped>
div.container {
  width: 100%;
  background-color: grey;
  color: darkred;
  padding: 10px;
}
#canvas-container {
  /* border: 1px solid black; */
  width: 100%;
}
#canvas {
  max-width: 100%;
}
</style>

<script>
import 'blob-polyfill';
import {saveAs} from 'file-saver';
import { setInterval } from 'timers';

export default {
  watch: {
    kanji: function() {
      this.drawCanvas();
    },
    english: function() {
      this.drawCanvas();
    },
    transparent: function() {
      this.drawCanvas();
    },
    use720p: function() {
      this.drawCanvas();
    },

  },
  data() {
    return {
      kanji: "死",
      english: "DEATH",
      transparent: false,
      use720p: false
    };
  },
  methods: {
    drawCanvas() {
      var canvas = document.getElementById("canvas");
      canvas.width = 1920;
      canvas.height = 1080;
      var kanjiSize = 350;
      var englishSize = 40;
      var kanjiOffset = 120;
      var englishOffset = 140;
      if(this.use720p) {
        canvas.width = 1280;
        canvas.height = 720;
        kanjiSize = Math.round(kanjiSize /1080 * 720);
        englishSize = Math.round(englishSize /1080 * 720);
        kanjiOffset = Math.round(kanjiOffset /1080 * 720);
        englishOffset = Math.ceil(englishOffset /1080 * 720);
      }
      var ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      if(this.transparent) {
        ctx.fillStyle = "rgba(0,0,0,0)";
      } else {
        ctx.fillStyle = "rgb(0,0,0)";
      }
      ctx.fillRect(0, 0, 1920, 1080);

      ctx.font = "" + kanjiSize + "px DFKai-sb,cwTeXKai,serif";

      var my_gradient = ctx.createLinearGradient(0, 0, 0, 170);
      my_gradient.addColorStop(0, "rgb(187,43,43)");
      my_gradient.addColorStop(1, "white");

      ctx.fillStyle = "#ba2c2b";
      var textWidth = ctx.measureText(this.kanji).width,
        textHeight = ctx.measureText(this.kanji).height;

      ctx.filter = "blur(1px)";

      for (let i = 0; i < 10; i++) {
        ctx.shadowColor = "RGBA(187, 80, 80," + (10 - i) / 20 + ")";
        ctx.shadowOffsetX = 0;
        ctx.shadowOffsetY = 0;
        ctx.shadowBlur = i * (this.use720p ? 2 : 3);
        ctx.fillText(
          this.kanji,
          (canvas.width - textWidth) / 2,
          (canvas.height - kanjiSize) / 2 + kanjiSize - kanjiOffset
        );
      }

      var english = this.english.toUpperCase().split("").join("　");
      ctx.font = "" + englishSize + "px serif";
      (textWidth = ctx.measureText(english).width),
        (textHeight = ctx.measureText(english).height);
      ctx.filter = "blur(1px)";
      ctx.fillText(
        english,
        (canvas.width - textWidth) / 2,
        (canvas.height - 30) / 2 + englishSize + englishOffset
      );
    },
    downloadCanvas(){
      var self = this;
      document.getElementById("canvas").toBlob(function(blob){
        saveAs(blob, self.english + ".png");
      })
    }
  },
  mounted() {
    this.drawCanvas();
    setInterval(() => {
      this.drawCanvas();
    }, 3000);
  }
};
</script>
