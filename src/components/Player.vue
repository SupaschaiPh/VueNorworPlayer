<script setup lang="ts">
import { onMounted, ref } from "vue";
import "normalize.css";
const videoElement = ref<HTMLVideoElement | null>(null);
const currentTime = ref<number | undefined>(0);
const duration = ref<number | undefined>(0);

function changeTimeHandler() {
  if (videoElement.value) {
    videoElement.value.currentTime = currentTime.value as number;
  }
}

function playHandler() {
    console.log("play")
  if (videoElement.value) {
    if (!videoElement.value.paused) {
      videoElement.value.pause();
    } else {
      videoElement.value.play();
    }
  }
}
onMounted(() => {
  if (videoElement.value) {
    videoElement.value.ontimeupdate = () => {
      currentTime.value = videoElement.value?.currentTime;
    };
    videoElement.value.controls = true;
    duration.value = videoElement.value.duration;
    videoElement.value.onloadeddata = (ev) => {
      console.log(videoElement.value.duration);
      duration.value = videoElement.value.duration;
    };
  }
});
</script>

<template>
  {{ currentTime }} {{ duration }}
  <section class="player-container">
    <video
      class="player-video-element"
      ref="videoElement"
      src="/BigBuckBunny.mp4"
    ></video>
    <div class="player-video-controler">
      <div></div>
      <div></div>
      <div>
        <div style="display: flex; flex-direction: column">
          <span>
            <v-btn @click="playHandler" rounded="lg"></v-btn>
          </span>
          <v-slider
            :max="duration"
            v-model="currentTime"
            @update:modelValue="changeTimeHandler"
            color="white"
          ></v-slider>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.player-container {
  position: relative;
  height: fit-content;
  width: fit-content;
  max-width: 100%;
  max-height: 100%;
}
.player-video-controler {
  position: absolute;
  height: 100%;
  width: 100%;
  top: 0;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  padding: 1rem;
}
.player-video-controler > div:first-child {
  flex-grow: 1;
}
.player-video-controler > div:nth-child(2) {
  flex-grow: 90;
}
.player-video-controler > div:last-child {
  flex-grow: 1;
}
</style>
