<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated >
      <q-toolbar>
        
        <q-toolbar-title>
          WebCam Demo
        </q-toolbar-title>

        <q-btn
          v-if="devices.length > 1"
          flat
          @click="changeCamera"
          icon-right="flip_camera_ios"
          class="absolute-right" />

      </q-toolbar>
    </q-header>

    <q-footer>

      <q-tabs
        dense
        class="bg-grey-2 text-teal"
      >
        <q-tab name="mails" label="Open"  @click="onStart" />
        <q-tab name="alarms" icon="camera" @click="onCapture" />
        <q-tab name="movies" label="Close" @click="onStop" />
      </q-tabs>

      <div class="absolute-bottom text-center no-pointer-events" >
        <q-btn
          @click="onCapture"
          round
          class="all-pointer-events"
          color="primary"
          size="24px"
          icon="camera"
        />
      </div>
      <!-- <q-btn-group spread>
        <q-btn color="secondary" label="Open" @click="onStart"/>
        <q-btn color="primary"  icon="camera" @click="onCapture"/>
        <q-btn color="red" label="Close" @click="onStop"/>
      </q-btn-group> -->
    </q-footer>

    <q-page-container>
      <div class="q-pa-md q-gutter-sm text-center">
        <h5 class="">Current Camera</h5>
        <span v-if="device">{{ device.label }}</span>
      </div>

      <web-cam
          ref="webcam"
          :device-id="deviceId"
          width="100%"
          @started="onStarted"
          @stopped="onStopped"
          @error="onError"
          @cameras="onCameras"
          @camera-change="onCameraChange"
      />

      <div class="q-pa-md q-gutter-sm text-center">
        <h5>Captured Image</h5>
      </div>
      <figure class="figure">
          <q-img :src="img" class="img-responsive" />
      </figure>

      
    </q-page-container>
  </q-layout>
</template>

<script>
import { WebCam } from "vue-web-cam";

export default {
  name: 'LayoutDefault',

  data() {
      return {
          img: null,
          camera: null,
          deviceId: null,
          devices: [],
          cameraIndex: 0
      }
  },
  computed: {
      device: function() {
          return this.devices.find(n => n.deviceId === this.deviceId);
      }
  },
  watch: {
      camera: function(id) {
          this.deviceId = id;
      },
      devices: function() {
          // Once we have a list select the first one
          const [first, ...tail] = this.devices;
          if (first) {
              this.camera = first.deviceId;
              this.deviceId = first.deviceId;
          }
          console.log(tail)
      }
  },
  methods: {
      onCapture() {
          this.img = this.$refs.webcam.capture();
      },
      onStarted(stream) {
          console.log("On Started Event", stream);
      },
      onStopped(stream) {
          console.log("On Stopped Event", stream);
      },
      onStop() {
          this.$refs.webcam.stop();
      },
      onStart() {
          this.$refs.webcam.start();
      },
      onError(error) {
          console.log("On Error Event", error)
      },
      onCameras(cameras) {
          this.devices = cameras;
          console.log("On Cameras Event", cameras)
      },
      onCameraChange(deviceId) {
          this.deviceId = deviceId;
          this.camera = deviceId;
          console.log("On Camera Change Event", deviceId)
      },
      changeCamera() {
        if((this.cameraIndex += 1) >= this.devices.length){
          this.cameraIndex = 0
        }
        this.camera = this.devices[this.cameraIndex].deviceId
        this.deviceId = this.devices[this.cameraIndex].deviceId

      }
  },
  components: {
    'web-cam': WebCam
  }
}
</script>

<style>
  h5 {
    margin: 5px;
  }
  .img-responsive {
    max-width: 100%;
  }
</style>
