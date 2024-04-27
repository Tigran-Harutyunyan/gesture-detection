<script setup>
import { ref, onMounted } from "vue";
import { drawHand } from "../utils/index";

const webcamRef = ref(null);
const canvasRef = ref(null);

const runHandpose = async () => {
  const net = await handpose.load();
  //  Loop and detect hands
  setInterval(() => {
    detect(net);
  }, 100);
};

const detect = async (net) => {
  // Check data is available

  if (
    typeof webcamRef.value !== "undefined" &&
    webcamRef.value !== null &&
    webcamRef.value.video.readyState === 4
  ) {
    // Get Video Properties
    const video = webcamRef.value.video;
    const videoWidth = webcamRef.value.video.videoWidth;
    const videoHeight = webcamRef.value.video.videoHeight;

    // Set video width
    webcamRef.value.video.width = videoWidth;
    webcamRef.value.video.height = videoHeight;

    // Set canvas height and width
    canvasRef.value.width = videoWidth;
    canvasRef.value.height = videoHeight;

    // Make Detections
    const hand = await net.estimateHands(video);
    console.log(hand);

    // Draw mesh
    const ctx = canvasRef.value.getContext("2d");
    drawHand(hand, ctx);
  }
};

onMounted(() => {
  runHandpose();
});
</script>

<template>
  <div class="app">
    <camera :resolution="{ width: 978, height: 652 }" autoplay ref="webcamRef"
      ><canvas ref="canvasRef"
    /></camera>
  </div>
</template>

<style>
#camera-container {
  position: absolute !important;
  margin-left: auto !important;
  margin-right: auto !important;
  left: 0;
  right: 0;
  text-align: center;
  z-index: 9;
  width: 978px !important;
  height: 652px !important;
}

.app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
