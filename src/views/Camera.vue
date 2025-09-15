<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const videoRef = ref(null);
const photo = ref(null);
let stream = null;

onMounted(async () => {
  try {
    stream = await navigator.mediaDevices.getUserMedia({
      video: {
        facingMode: "user", // front-facing camera
      },
    });
    if (videoRef.value) {
      videoRef.value.srcObject = stream;
    }
  } catch (err) {
    console.error("Error accessing camera:", err);
  }
});

onBeforeUnmount(() => {
  if (stream) {
    stream.getTracks().forEach((track) => track.stop());
  }
});

function capture() {
  const video = videoRef.value;
  if (!video) return;

  const size = 300;
  const canvas = document.createElement("canvas");
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext("2d");

  const videoAspect = video.videoWidth / video.videoHeight;
  let sx, sy, sw, sh;

  if (videoAspect > 1) {
    // wider than tall
    sh = video.videoHeight;
    sw = sh;
    sx = (video.videoWidth - sw) / 2;
    sy = 0;
  } else {
    // taller than wide
    sw = video.videoWidth;
    sh = sw;
    sx = 0;
    sy = (video.videoHeight - sh) / 2;
  }

  ctx.drawImage(video, sx, sy, sw, sh, 0, 0, size, size);
  photo.value = canvas.toDataURL("image/png");
}

function retake() {
  photo.value = null;
}
</script>

<template>
  <div class="camera-wrapper">
    <div class="camera-header">
      <h2 class="title">
        <span style="font-weight: 400;">TAKE A </span>SELFIE
      </h2>
      <p class="subtitle">
        Center your face in the circle and hold still.
      </p>
    </div>

    <div class="camera-box">
      <video v-if="!photo" ref="videoRef" autoplay playsinline muted></video>
      <img v-else :src="photo" alt="Your selfie" />
    </div>

    <div class="controls" v-if="!photo">
      <button class="btn capture-btn" @click="capture">
        Capture
      </button>
    </div>

    <div class="controls" v-else>
      <button class="btn retake-btn" @click="retake">
        Retake
      </button>
      <button class="btn confirm-btn" @click="$router.push('/')">
        Use this photo
      </button>
    </div>
  </div>
</template>


<style scoped>
.camera-wrapper {
  background-color: #f7f8fc;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 24px;
  min-height: 100vh;
  padding: 20px;
  font-family: 'Manrope', 'Noto Sans', sans-serif;
  box-sizing: border-box;
}

.camera-header {
  text-align: center;
}

.title {
  font-size: 32px;
  font-weight: 800;
  color: #1e293b;
  margin: 0 0 8px;
}

.subtitle {
  margin: 0 auto;
  max-width: 350px;
  line-height: 1.5;
  font-size: 15px;
  color: #64748b;
}

.camera-box {
  width: 300px;
  height: 300px;
  overflow: hidden;
  border-radius: 50%;
  background: #222;
  border: 6px solid #e0e7ff;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
  display: flex;
  justify-content: center;
  align-items: center;
}

.camera-box video,
.camera-box img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.camera-box video {
    transform: scaleX(-1);
}

.controls {
  display: flex;
  justify-content: center;
  gap: 16px;
  width: 100%;
  max-width: 350px;
}

.btn {
  font-family: 'Manrope', sans-serif;
  font-size: 16px;
  padding: 14px 24px;
  font-weight: 600;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
  flex-grow: 1;
}

.btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.capture-btn,
.confirm-btn {
  background-color: #4f46e5;
  color: #ffffff;
}

.capture-btn:hover,
.confirm-btn:hover {
  background-color: #4338ca;
}

.retake-btn {
  background-color: #ffffff;
  color: #475569;
  border: 1px solid #cbd5e1;
}

.retake-btn:hover {
  background-color: #f8fafc;
  border-color: #94a3b8;
}
</style>