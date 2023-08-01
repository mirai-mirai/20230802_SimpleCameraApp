<script setup lang="ts">
import { ref } from 'vue'

const $video = ref<HTMLVideoElement>()
const $startBtn = ref<HTMLButtonElement>()
const $stopBtn = ref<HTMLButtonElement>()
let stream: MediaStream

const startVideo = async () => {
  const cfg = { video: true, audio: false }
  stream = await navigator.mediaDevices.getUserMedia(cfg)
  $video.value!.srcObject = stream
  $video.value!.play()
}

const stopVideo = () => {
  stream.getTracks().forEach(track => track.stop())
  $video.value!.pause()
  $video.value!.srcObject = null
}

window.onload = async () => {
  console.log('window.onload')
  $startBtn.value!.addEventListener('click', startVideo)
  $stopBtn.value!.addEventListener('click', stopVideo)
}

</script>

<template>
  <h1>Simple Camera</h1>
  <h5>-- T.shiozaki 2023/8/2 --</h5>

  <button ref="$startBtn">start</button>
  <button ref="$stopBtn">stop</button> <br>
  <video ref="$video" autoplay></video>
</template>

<style scoped>
h1 {
  margin-bottom: 0;
}

h5 {
  margin-top: 0;
}

video {
  width: 320px;
  height: auto;
}
</style>
