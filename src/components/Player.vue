<script setup lang="ts">
import { onMounted, ref } from "vue";
import "normalize.css";
const videoElement = ref<HTMLVideoElement | null>(null);
const containerElement = ref<HTMLDivElement | null>(null);

const currentTime = ref<number | undefined>(0);
const duration = ref<number | undefined>(0);
const color = ref("white");
const holdVolume = ref(100);
const volume = ref(holdVolume.value);

const isPaused = ref(true);
const isFullscreen = ref(false);
const isSetting = ref(false)

function changeTimeHandler() {
  if (videoElement.value) {
    videoElement.value.currentTime = currentTime.value as number;
  }
}

function changeVolumnHandler() {
  if (videoElement.value) {
    videoElement.value.volume = (volume.value as number) / 100;
  }
}

function playHandler() {
  if (videoElement.value) {
    if (!videoElement.value.paused) {
      videoElement.value.pause();
    } else {
      videoElement.value.play();
    }
    isPaused.value = videoElement.value.paused;
  }
}

function volumeToggleHandler() {
  if (videoElement.value) {
    if (videoElement.value.volume > 0) {
      holdVolume.value = volume.value;
      videoElement.value.volume = 0;
      volume.value = 0;
    } else {
      videoElement.value.volume = holdVolume.value / 100;
      volume.value = holdVolume.value;
    }
  }
}

function fullscreenHandler() {
  if (videoElement.value && containerElement.value) {
    if (isFullscreen.value) document?.exitFullscreen();
    else containerElement.value?.requestFullscreen();
    containerElement.value.onfullscreenchange = () => {
      isFullscreen.value = !isFullscreen.value;
    };
  }
}

function onKeypressHandler(e: KeyboardEvent) {
  if (e.key == " ") {
    playHandler();
  }
}

onMounted(() => {
  if (videoElement.value) {
    videoElement.value.ontimeupdate = () => {
      currentTime.value = videoElement.value?.currentTime;
    };
    videoElement.value.onloadeddata = () => {
      if (videoElement.value) duration.value = videoElement.value.duration;
    };

    duration.value = videoElement.value.duration;
    isPaused.value = videoElement.value.paused;
  }
  document
    .querySelector("div.player-bottom-bar-volumn div.v-input__details")
    ?.remove();

  document.addEventListener("keypress", onKeypressHandler);
});
</script>

<template>
  <section class="player-container" ref="containerElement">
    <video
      class="player-video-element"
      ref="videoElement"
      src="/BigBuckBunny.mp4"
      style="width: 100%; height: 100%"
    ></video>
    <div class="player-video-controler">
      <div></div>
      <div @click="playHandler()" class="flexy" style="justify-content: center;">
        <v-btn
                    :size="69"
                    :color="color"
                    density="compact"
                    @click="playHandler"
                    rounded="lg"
                    variant="tonal"
                  >
                <v-icon size="32">
                  {{ isPaused ? 'mdi-play' : 'mdi-pause' }}
                </v-icon>
                </v-btn>
      </div>
      <div>
        <div
          class="player-bottom-bar"
          style="display: flex; flex-direction: column"
        >
          <v-slider
            :max="duration"
            v-model="currentTime"
            @update:modelValue="changeTimeHandler"
            :color="color"
          >
            <template v-slot:details>
              <div
                class="flexy"
                style="width: 100%; justify-content: space-between"
              >
                <div class="flexy">
                  <v-btn
                    :color="color"
                    :icon="isPaused ? 'mdi-play' : 'mdi-pause'"
                    density="compact"
                    @click="playHandler"
                    rounded="lg"
                    variant="text"
                  ></v-btn>
                  <v-hover>
                    <template v-slot:default="{ isHovering, props }">
                      <span
                        v-bind="props"
                        :style="
                          'display: flex; align-items: center; ' +
                          (isHovering ? 'width: 6rem;' : '')
                        "
                      >
                        <v-btn
                          :color="color"
                          :icon="
                            volume != 0
                              ? 'mdi-volume-high'
                              : 'mdi-volume-variant-off'
                          "
                          density="compact"
                          @click="volumeToggleHandler"
                          rounded="lg"
                          variant="text"
                        ></v-btn>
                        <span
                          :style="
                            !isHovering
                              ? 'width:0px;overflow: hidden;'
                              : 'width:100%'
                          "
                        >
                          <v-slider
                            v-bind="props"
                            min="0"
                            max="100"
                            v-model="volume"
                            class="player-bottom-bar-volumn"
                            :color="color"
                            density="compact"
                            :thumb-size="16"
                            @update:model-value="changeVolumnHandler"
                          ></v-slider>
                        </span>
                      </span>
                    </template>
                  </v-hover>
                  <p style="text-shadow: 0 0 2px black">
                    {{ ((currentTime as number) / 60).toFixed(2) }}
                    /
                    {{ ((duration as number) / 60).toFixed(2) }}
                  </p>
                </div>
                <div class="flexy">
                  <div v-if="isSetting">
                      <v-list style="position: absolute;bottom:50px" rounded="xl" :color="color">
                        <v-list-item density="compact" title="Playback speed">
                        </v-list-item>
                        <v-list-item
                          density="compact"
                          title="Resolution qulity"
                        >
                          <v-menu
                            opacity="100%"
                            activator="parent"
                            location="right"
                            style="position: absolute;"
                          >
                            <v-list rounded="xl" :color="color">
                              <v-list-item density="compact" title="1080p">
                              </v-list-item>
                              <v-list-item density="compact" title="720p">
                              </v-list-item>
                              <v-list-item density="compact" title="540p">
                              </v-list-item>
                            </v-list>
                          </v-menu>
                        </v-list-item>
                      </v-list>
                  </div>
                  <v-btn
                    :color="color"
                    icon="mdi-cog"
                    density="compact"
                    @click="()=>isSetting = !isSetting"
                    rounded="lg"
                    variant="text"
                    id="menu-activator"
                    
                  >
                  
                  </v-btn>
                  
                  <v-btn
                    :color="color"
                    icon="mdi-picture-in-picture-bottom-right"
                    density="compact"
                    @click="playHandler"
                    rounded="lg"
                    variant="text"
                  ></v-btn>
                  <v-btn
                    :color="color"
                    :icon="
                      isFullscreen ? 'mdi-fullscreen-exit' : 'mdi-fullscreen'
                    "
                    density="compact"
                    @click="fullscreenHandler"
                    rounded="lg"
                    variant="text"
                  ></v-btn>
                </div>
              </div>
            </template>
          </v-slider>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.player-container {
  position: relative;
  height: 100%;
  width: 100%;
  overflow: hidden;
}
.player-video-element {
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
  background-color: black;
}
.player-video-controler {
  position: absolute;
  height: 100%;
  width: 100%;
  top: 0;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}
.player-video-controler > div:first-child {
  flex-grow: 1;
}
.player-video-controler > div:nth-child(2) {
  flex-grow: 100;
}
.player-video-controler > div:last-child {
  flex-grow: 1;
}
.player-bottom-bar {
  padding: 5px;
}
div.player-bottom-bar-volumn div.v-input__details {
  display: none !important;
}
.flexy {
  display: flex;
  align-items: center;
  gap: 1rem;
}
</style>
