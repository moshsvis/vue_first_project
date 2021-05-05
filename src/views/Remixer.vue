<template>
  <div>
    <div class="row">
      <div class="col-md-6">
        <div id="waveformLeft"></div>
        <Button @click="play('left')">Play</Button>
        <Button @click="pause('left')">Pause</Button>
      </div>
      <div class="col-md-6">
        <div id="waveformRight"></div>
        <Button @click="play('right')">Play</Button>
        <Button @click="pause('right')">Pause</Button>
      </div>
    </div>
    <div class="row">
      <div class="col-md-12">
        <div id="equalizer">
          <!-- Here be equalizer sliders -->
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-12">
        <table class="table table-hover">
          <thead>
            <tr>
              <th scope="col">Track</th>
              <th scope="col">Remixer</th>
              <th scope="col">BPM</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>Intro - In the style of Dua Lipa Levitating</td>
              <td>Aaron Traylor</td>
              <td>124</td>
            </tr>
            <tr>
              <td>Intro - Drum and Bass</td>
              <td>Aaron Traylor</td>
              <td>120</td>
            </tr>
            <tr>
              <td>Intro - Disco</td>
              <td>Mike Villa</td>
              <td>129</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
var WaveSurfer = require("wavesurfer.js");


export default {
  data() {
    return {
      crates: null,
      cratecount: null,
    };
  },
  mounted() {
    this.wavesurferLeft = WaveSurfer.create({
      container: "#waveformLeft",
      waveColor: "violet",
      progressColor: "purple",
    });
    this.wavesurferLeft.load(
      "https://file-examples-com.github.io/uploads/2017/11/file_example_MP3_2MG.mp3"
    );

    this.wavesurferRight = WaveSurfer.create({
      container: "#waveformRight",
      waveColor: "green",
      progressColor: "lime",
    });
    this.wavesurferRight.load(
      "https://file-examples-com.github.io/uploads/2017/11/file_example_MP3_5MG.mp3"
    );
  },
  methods: {
    play: function (deck) {
      switch (deck) {
        case "left":
          this.wavesurferLeft.play();
          break;
        case "right":
          this.wavesurferRight.play();
          break;
      }
    },
    pause: function (deck) {
      switch (deck) {
        case "left":
          this.wavesurferLeft.pause();
          break;
        case "right":
          this.wavesurferRight.pause();
          break;
      }
    },
  },
};
</script>

<style>
</style>