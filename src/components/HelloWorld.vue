<template>
  <div style="padding: 50px 50px 50px 50px; border: 1px solid black">
    <p class="decode-result">
      Last result: <b>{{ result }}</b>
    </p>
    <qrcode-stream :camera="camera" @decode="onDecode" @init="onInit">
      <div v-if="validationSuccess" class="validation-success">Success</div>

      <div v-if="validationFailure" class="validation-failure">Ooops!</div>

      <div v-if="validationPending" class="validation-pending">
        validation in progress...
      </div>
    </qrcode-stream>
  </div>
</template>

<script>
import { QrcodeStream } from "vue-qrcode-reader";

export default {
  components: { QrcodeStream },

  data() {
    return {
      isValid: undefined,
      camera: "auto",
      result: null,
    };
  },

  computed: {
    validationPending() {
      return this.isValid === undefined && this.camera === "off";
    },

    validationSuccess() {
      return this.isValid === true;
    },

    validationFailure() {
      return this.isValid === false;
    },
  },

  methods: {
    onInit(promise) {
      promise.catch(console.error).then(this.resetValidationState);
    },

    resetValidationState() {
      this.isValid = undefined;
    },

    async onDecode(content) {
      this.result = content;
      this.turnCameraOff();

      await this.timeout(3000);
      this.isValid = content.startsWith("http");

      await this.timeout(2000);
      this.turnCameraOn();
    },

    turnCameraOn() {
      this.camera = "auto";
    },

    turnCameraOff() {
      this.camera = "off";
    },

    timeout(ms) {
      return new Promise((resolve) => {
        window.setTimeout(resolve, ms);
      });
    },
    toLink() {
      if (this.result) {
        window.open(this.result);
      }
    },
  },
};
</script>

<style scoped>
.validation-success,
.validation-failure,
.validation-pending {
  position: absolute;
  width: 100%;
  height: 100%;

  background-color: rgba(255, 255, 255, 0.8);
  text-align: center;
  font-weight: bold;
  font-size: 1rem;
  padding: 10px;

  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
}
.validation-success {
  color: green;
}
.validation-failure {
  color: red;
}
</style>