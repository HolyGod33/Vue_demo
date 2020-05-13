<template>
  <div class="testTracking">

    <video id="video" width="318" height="270" preload autoplay loop muted />
    <canvas id="canvas" width="318" height="270" />
    <div class="buttonDiv">
      <button type="button" @click="submit">上传照片</button>
      <button type="button" name="button" @click="openCamera">点击我拍照</button>
    </div>
  </div>
</template>

<script>
require('tracking/build/tracking-min.js')
require('tracking/build/data/face-min.js')
require('tracking/build/data/mouth-min.js')
require('tracking/examples/assets/stats.min.js')

export default {
  name: 'TestTracking',
  data() {
    return {

    }
  },
  destroyed() {
    // 停止侦测
    this.trackerTask.stop()
    // 关闭摄像头
    this.trackerTask.closeCamera()
  },
  methods: {
    openCamera() {
      var video = document.getElementById('video')
      var canvas = document.getElementById('canvas')
      var context = canvas.getContext('2d')

      var tracker = new tracking.ObjectTracker('face')
      tracker.setInitialScale(4)
      tracker.setStepSize(2)
      tracker.setEdgesDensity(0.1)

      this.trackerTask = tracking.track('#video', tracker, { camera: true })

      tracker.on('track', function(event) {
        context.clearRect(0, 0, canvas.width, canvas.height)

        event.data.forEach(function(rect) {
          context.font = '11px Helvetica'
          context.fillText('已识别到人脸，请点击拍照', 100, 40)
          context.strokeStyle = '#a64ceb'
          context.strokeRect(rect.x, rect.y, rect.width, rect.height)
        })
      })
    },
    submit() {
      const that = this
      const canvas = document.getElementById('canvas')
      const context = canvas.getContext('2d')
      const video = document.getElementById('video')
      context.drawImage(video, 0, 0, 500, 400)
      canvas.toBlob((blob) => {
        axios.post({ faceUrl: URL.createObjectURL(blob) })
          .then((res) => {
            console.log('上传成功')
          })
      })
    }
  }
}

</script>

<style lang="scss" scoped>


</style>
