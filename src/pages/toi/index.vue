<template>
    <main>
        <h1>TOI受付</h1>
        <div id="Qrcode" class="contents" v-bind:class="{ 'hidden': !qrMode }"><!-- QRコードリーダーを使う場合 -->
            <button @click="turnCameraOn()" class="cameraBtn">カメラ起動</button>
            <button @click="turnCameraOff()" class="cameraBtn">カメラ停止</button>
            <p class="qrResult">{{ result }}</p><!-- QRコード読み取り結果を表示 -->
            <p v-if="cameraPre">カメラに接続中</p><!-- 'cameraPre=true'のとき表示 -->
            <p v-if="cameraError">カメラに接続できませんでした</p><!-- 'cameraError=true'のとき表示 -->
            <p>{{ error }}</p><!-- errorを表示 -->
            <qrcode-stream :camera="camera" @decode="onDecode" @init="onInit" class="qrstream" v-bind:class="{ 'blur': beBlur }"></qrcode-stream><!-- vue-qrcode-readerの設定およびカメラから取得した映像の表示 -->
            <button class="chBtn" @click="manChange()">手動で入力する</button><!-- くにナンバー入力方法の変更ボタン -->
        </div>
        <div id="Manual" class="contents" v-bind:class="{ 'hidden': qrMode }"><!-- 手動で入力する場合 -->
            <form>
                <input type="number" name="inputNum" id="inputNum" placeholder="４桁番号">
                <p id="notNumMsg" hidden>正しく学籍番号を入力してください。</p>
                <button type="button" id="preBtn" @click="prep()">送信</button>
            </form>
            <button type="button" class="chBtn" @click="qrChange()">QRコードを読み取る</button>
        </div>
    </main>
</template>

<script>
import { QrcodeStream } from 'vue-qrcode-reader'
//'vue-qrcode-reader'を利用。使い方はREADME、'https://gruhn.github.io/vue-qrcode-reader/demos/CustomTracking.html'などを参照。

export default {
    components: { QrcodeStream },

  //変数などを定義する。
  data () {
    return {
      isValid: undefined,
      camera: 'auto',
      result: null,
      cameraPre: true,
      cameraError: false,
      error: '',
      qrMode: true,
      beBlur: false,
      sendData : {
        kuninum: null,
      },
    }
  },

  //関数などを定義する。
  methods: {
    //-------------------------------------------------------------------------------------
    //qrcode関連

    //カメラに接続
    async onInit (promise) {
      try {
        await promise
        this.cameraPre = false;
      } catch (error) {//エラーが発生した時
        this.cameraPre = false;
        this.cameraError = true;
        this.camera = 'off';
      }
    },

    //QRコードを認識した時
    async onDecode (content) {
        this.result = content;
        this.sendData.kuninum = content;
        console.log(this.result);
        this.prep();
    },

    //カメラをオンにする
    turnCameraOn () {
      this.camera = 'auto';
      this.beBlur = false;//映像のぼかしをなくす
    },

    //カメラをオフにする
    turnCameraOff () {
      this.camera = 'off';
      this.beBlur = true;
    },
    
    //
    //-------------------------------------------------------------------------------------
    //その他

    //手動入力モードに変更する
    manChange(){
      this.qrMode = false;
      this.turnCameraOff();
    },

    //QRコードモードに変更する
    qrChange(){
      this.qrMode = true;
      this.turnCameraOn();
    },

    //
    //-------------------------------------------------------------------------------------
    //検証・送信
    
    //入力された番号が正しい形式か判断する。
    prep(){
        this.sendData.kuninum = document.getElementById('inputNum').value;
        console.log(this.sendData.kuninum);
      if(this.sendData.kuninum.match(/^[^\W0IOl]{3}[ -]?[^\W0IOl]{1}[ -]?[^\W0IOl]{1}$/)){//正しければ送信する関数を実行。
        this.sendFun();
      }else{//正しくなければメッセージを一時的に表示。
        alert('くにナンバーの形式に一致しませんでした')
      }
    },

    //送信する関数。JSONにして送る。エラーが返ってきたらページに表示する。
    sendFun (){
      //以下去年使用したもの。
      //try{
      //  let data = await fetch('/staff/TOI_S', {
      //    method: "POST",
      //    headers: {
      //      "Content-Type": "application/json"
      //    },
      //    body: JSON.stringify(sendData)
      //  })
      //  .then(response => response.json());
      //  console.log(data);
      //  if(data.success){
      //    alert(`受付処理が完了しました。\n４桁番号：${sendData.kuninum}`)
      //  } else if (data.error === "Server Error") {
      //    alert("サーバーでエラーが発生しました。もう一度お願いします。");
      //  } else if(data.error === "Invalid JSON") {
      //    alert("データの送信形式が違います。何度もこの表示が出る場合は担当者にお知らせください。");
      //  } else if(data.error === "Invalid Kuninum") {
      //    alert("学籍番号の形式が違います。もう一度確認してください。");
      //  }else{
      //    alert("予期しないエラーです。")
      //  }
      //} catch (err){
      //  alert("サーバーとの通信に失敗しました。再度試してください");
      //}
      //reset();
    },

    //送信後変数や入力欄をリセットする。
    reset(){
      this.result ='';
      this.sendData.kuninum = '';
    }
  }
}
</script>

<!-- このページで使うスタイルの設定 -->
<style scoped>
  /*----- 全体　----*/
  body{
    max-width: 500px;
    margin: 30px auto;
  }
  h1 {
    margin: 10px 40px;
    font-size: 40px;
    text-align: center;
    color: var(--on-sfv);
  }

  /*----- QRコードモード　----*/
  #Qrcode{
    width: 85vw;
    max-width: 800px;
    margin: auto;
    margin-top: 50px;
  }
  .qrstream{
      width: 85%;
      max-width: 700px;
  }
  .cameraBtn{
    margin: 10px 0 30px auto;
    padding: 5px 15px;
    font-size: 10px;
    color: #fff;
    border: 0;
    border-radius: 10px;
    background-color: rgb(144, 200, 226);
  }
  .cameraBtn:hover{background-color: rgb(112, 184, 218);}
  .blur{
    filter: blur(8px);
  }

  /*----- 手動モード　----*/
  form{
    margin: auto;
  }
  #Manual{
    width: 85%;
    max-width: 800px;
    margin: auto;
    margin-top: 50px;
  }
  #inputNum{
    display: block;
    font-size: 30px;
    text-align: center;
    width: 90%;
    border: none;
    outline: none;
    border-radius: 5px;
    padding: 10px 15px;
    margin: auto;
    background-color: rgb(234, 241, 255);
  }
  #preBtn{
    display: block;
    color: var(--bl);
    font-size: 20px;
    border: 0;
    outline: 0;
    padding: 5px 10px;
    margin-top: 20px;
    margin-left: auto;
    background-color: var(--bg);
  }

  /*----- 共通　----*/
  input::placeholder{
    color: skyblue;
  }
  .chBtn{
    display: block;
    color: var(--on-sfv);
    font-size: 15px;
    border: none;
    outline: none;
    padding: 10px, 20px;
    margin-top: 30px;
    margin-left: auto;
    background-color: var(--bg);
  }
  .hidden{
    display: none;
  }
</style>