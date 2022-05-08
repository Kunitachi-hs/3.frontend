<template>
    <main>
        <h1>TOI受付</h1>
        <div id="Qrcode"><!-- QRコードリーダーを使う場合 -->
            <button @click="turnCameraOn()">カメラ起動</button>
            <button @click="turnCameraOff()">カメラ停止</button>
            <p v-if="cameraError">カメラに接続できません</p>
            <p>{{ error }}</p>
            <qrcode-stream :camera="camera" @decode="onDecode" @init="onInit"></qrcode-stream>
            <div class="manual"></div>
            <button class="chBtn" @click="manChange()">手動で入力する</button>
        </div>
        <div id="Manual" hidden><!-- 手動で入力する場合 -->
            <form>
                <input type="number" name="inputNum" id="inputNum" placeholder="４桁番号">
                <p id="notNumMsg" hidden>正しく学籍番号を入力してください。</p>
                <button type="button" id="preBtn">送信</button>
            </form>
            <button type="button" class="chBtn" @click="qrChange()">QRコードを読み取る</button>
        </div>
    </main>
</template>

<script>
import { QrcodeStream } from 'vue-qrcode-reader'

export default {
    components: { QrcodeStream },

  data () {
    return {
      isValid: undefined,
      camera: 'auto',
      result: null,
      cameraError: true,
      error: '',
    }
  },

  methods: {

    async onInit (promise) {
      try {
        await promise
      } catch (error) {
        if (error.name === 'NotAllowedError') {
          this.error = "ERROR: you need to grant camera access permission"
        } else if (error.name === 'NotFoundError') {
          this.error = "ERROR: no camera on this device"
        } else if (error.name === 'NotSupportedError') {
          this.error = "ERROR: secure context required (HTTPS, localhost)"
        } else if (error.name === 'NotReadableError') {
          this.error = "ERROR: is the camera already in use?"
        } else if (error.name === 'OverconstrainedError') {
          this.error = "ERROR: installed cameras are not suitable"
        } else if (error.name === 'StreamApiNotSupportedError') {
          this.error = "ERROR: Stream API is not supported in this browser"
        } else if (error.name === 'InsecureContextError') {
          this.error = 'ERROR: Camera access is only permitted in secure context. Use HTTPS or localhost rather than HTTP.';
        } else {
          this.error = `ERROR: Camera error (${error.name})`;
        }
      }
    },

    resetValidationState () {
        this.isValid = undefined
    },

    async onDecode (content) {
        this.result = content;
        this.turnCameraOff();
        this.turnCameraOn;
    },

    turnCameraOn () {
      this.camera = 'auto'
    },

    turnCameraOff () {
      this.camera = 'off'
    },

    timeout (ms) {
      return new Promise(resolve => {
        window.setTimeout(resolve, ms)
      })
    }
    }
}
</script>

<style scoped>
    qrcode-stream{
        width: 300;
    }
</style>