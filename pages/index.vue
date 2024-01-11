<template>
  <div>
    <video ref="video" autoplay></video>
    <button @click="capturePhoto">Capture Photo</button>
    <canvas ref="canvas" style="display: none"></canvas>
    <img v-if="capturedImage" :src="capturedImage" alt="Captured" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      capturedImage: null,
    };
  },
  methods: {
    async capturePhoto() {
      const video = this.$refs.video;
      const canvas = this.$refs.canvas;
      const context = canvas.getContext("2d");
      navigator.mediaDevices
        .getUserMedia({ video: true })
        .then((stream) => {
          video.srcObject = stream;

          video.onloadedmetadata = () => {
            context.drawImage(video, 0, 0, canvas.width, canvas.height);

            // Convert the canvas content to base64 data
            this.capturedImage = canvas.toDataURL("image/png");

            // Stop the video stream
            stream.getTracks().forEach((track) => track.stop());
            video.play();
          };
        })
        .catch((error) => {
          console.error("Error accessing camera:", error);
        });
    },
  },
};
</script>
