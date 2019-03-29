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
              <button @click="downloadCanvas">Download</button>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

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
import {saveAs} from 'file-saver';

export default {
  watch: {
    kanji: function() {
      this.drawCanvas();
    },
    english: function() {
      this.drawCanvas();
    }
  },
  data() {
    return {
      kanji: "死",
      english: "DEATH"
    };
  },
  methods: {
    drawCanvas() {
      var canvas = document.getElementById("canvas");
      var ctx = canvas.getContext("2d");

      ctx.fillStyle = "rgb(0,0,0)";
      ctx.fillRect(0, 0, 1920, 1080);

      var kanjiSize = 350;
      ctx.font = "" + kanjiSize + "px DFKai-sb";

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
        ctx.shadowBlur = i * 3;
        ctx.fillText(
          this.kanji,
          (canvas.width - textWidth) / 2,
          (canvas.height - kanjiSize) / 2 + kanjiSize - 120
        );
      }

      var english = this.english.toUpperCase().split("").join("　");
      ctx.font = "40px serif";
      (textWidth = ctx.measureText(english).width),
        (textHeight = ctx.measureText(english).height);
      ctx.filter = "blur(1px)";
      ctx.fillText(
        english,
        (canvas.width - textWidth) / 2,
        (canvas.height - 30) / 2 + 40 + 140
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
  }
};
</script>
