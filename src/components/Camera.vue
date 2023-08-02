<script setup lang="ts">
import { ref } from 'vue'

const $video = ref<HTMLVideoElement>()
const $startBtn = ref<HTMLButtonElement>()
const $stopBtn = ref<HTMLButtonElement>()
const $devices = ref<MediaDeviceInfo[]>([])
const selectedDevId = ref<string | null>(null)
let stream: MediaStream

const startVideo = async () => {
  const deviceId = selectedDevId.value as ConstrainDOMString
  const cfg: MediaStreamConstraints = { video: { deviceId }, audio: false }
  stream = await navigator.mediaDevices.getUserMedia(cfg)
  $video.value!.srcObject = stream
  $video.value!.play()
}

const stopVideo = () => {
  if (!stream) return
  stream.getTracks().forEach(track => track.stop())

  $video.value!.pause()
  $video.value!.srcObject = null
}

const getDevices = async () => {
  const devices = await navigator.mediaDevices.enumerateDevices()
  $devices.value = devices.filter(({ kind }) => kind === 'videoinput');
  selectedDevId.value = $devices.value[0].deviceId
}

const changeDevice = () => {
  const nowPlaying = !$video.value!.paused
  stopVideo()
  if (nowPlaying) startVideo()
}

window.onload = async () => {
  $startBtn.value!.addEventListener('click', startVideo)
  $stopBtn.value!.addEventListener('click', stopVideo)
  getDevices()
}

</script>

<template>
  <h1>Simple Camera</h1>
  <h5>-- T.shiozaki 2023/8/2 --</h5>
  Select Camera:
  <div v-for="{ deviceId, label } in $devices" :key="deviceId">
    <input type="radio" v-model="selectedDevId" :value="deviceId" @change="changeDevice">
    <label>{{ label }}</label>
  </div>
  <button ref="$startBtn">start</button>
  <button ref="$stopBtn">stop</button> <br>
  <video ref="$video" autoplay></video>
</template>

<style scoped>
h1 {
  margin: 0px 0px 0px 0px;
}

h5 {
  margin: 0px 0px 10px 0px;
}

video {
  width: 320px;
  height: auto;
}
</style>
