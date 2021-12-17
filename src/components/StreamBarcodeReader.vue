<template>
    <div class="scanner" v-show="!isLoading">
      <video poster="data:image/gif,AAAA" ref="scanner"></video>
    </div>
</template>

<script>
import { BrowserMultiFormatReader, Exception } from "@zxing/library";
export default {
  name: "stream-barcode-reader",
  data() {
    return {
      isLoading: false,
      codeReader: new BrowserMultiFormatReader(),
      isMediaStreamAPISupported:
        navigator &&
        navigator.mediaDevices &&
        "enumerateDevices" in navigator.mediaDevices
    };
  },
  mounted() {
    if (!this.isMediaStreamAPISupported) {
      throw new Exception("Media Stream API is not supported");
      return;
    }
    this.start();
    this.codeReader.decodeFromVideoDevice(
        undefined,
        this.$refs.scanner)
    this.isLoading = false;
    this.$refs.scanner.oncanplay = event => {
      this.$emit("loaded");
    };
  },
  beforeUnmount() {
    this.codeReader.reset();
  },
  methods: {
    start() {
      this.codeReader.decodeFromVideoDevice(
        undefined,
        this.$refs.scanner,
        (result, err) => {
          if (result) {
            this.$emit("decode", result.text);
          }
        }
      );
    }
  }
};
</script>

<style scoped>
video {
  position: absolute;
  bottom: 0%;
  z-index: -1;
}
.overlay-element {
  position: absolute;
  top: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  background: rgba(30, 30, 30, 0.5);
  -webkit-clip-path: polygon(
    0% 0%,
    0% 100%,
    20% 100%,
    20% 20%,
    80% 20%,
    80% 80%,
    20% 80%,
    20% 100%,
    100% 100%,
    100% 0%
  );
  clip-path: polygon(
    0% 0%,
    0% 100%,
    20% 100%,
    20% 20%,
    80% 20%,
    80% 80%,
    20% 80%,
    20% 100%,
    100% 100%,
    100% 0%
  );
}
.laser {
  width: 60%;
  margin-left: 20%;
  background-color: tomato;
  height: 1px;
  position: absolute;
  top: 40%;
  z-index: 2;
  box-shadow: 0 0 4px red;
  -webkit-animation: scanning 2s infinite;
  animation: scanning 2s infinite; 
}
@-webkit-keyframes scanning {
  50% {
    -webkit-transform: translateY(75px);
    transform: translateY(75px);
  }
}
@keyframes scanning {
  50% {
    -webkit-transform: translateY(75px);
    transform: translateY(75px);
  }
}


</style>