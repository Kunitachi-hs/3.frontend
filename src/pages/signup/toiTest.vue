<!--template>
    <h1>TOI 受付</h1>
    <div id="QRcode">
    <button onclick="startCamera();" id="startBtn" class="cameraBtn">カメラ起動</button>
    <button onclick="stopCamera();" id="stopBtn" class="cameraBtn">カメラ停止</button>
    <div id="cameraLoading">カメラに接続中...</div>
    <canvas id="canvas" hidden></canvas>
    <div id="output" hidden>No data detected</div>
    <button type="button" class="chBtn" onclick="manChange()">手動で入力する</button>
    </div>
    <div id="manual" hidden>
    <form onsubmit="prep(); return false;">
        <input type="number" name="inputNum" id="inputNum" placeholder="４桁番号">
        <p id="notNumMsg" hidden>正しく学籍番号を入力してください。</p>
        <button type="button" id="preBtn" onclick="prep()">送信</button>
    </form>
    <button type="button" class="chBtn" onclick="qrChange()">QRコードを読み取る</button>
    </div>
</template>
<script src="jsQR.js"></script>
<script>
const $  = document.querySelector.bind(document);
const $$ = document.querySelectorAll.bind(document);
//-----------------------------------------------------------------
let video = document.createElement("video");
let canvasElement = document.getElementById("canvas");
let canvas = canvasElement.getContext("2d");
let QRcode = document.getElementById("QRcode");
let cameraLoading = document.getElementById("cameraLoading");
let outputContainer = document.getElementById("output");
//-----------------------------------------------------------------
let manual = document.getElementById("manual");
let inputNum = document.getElementById("inputNum");
let notNumMsg = document.getElementById("notNumMsg");
//-----------------------------------------------------------------
let isReady = true;
let operation=false, id;
//-----------------------------------------------------------------
let stopBtn = document.getElementById("stopBtn");
let startBtn = document.getElementById("startBtn");
//-----------------------------------------------------------------
var tick_roop;
let preNum = 0;
let sendData = {
    class : null,
    role : "reception",
    kuninum: null
}
navi();

function navi(){
    navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } })
    .then(stream => {
    video.srcObject = stream;
    video.setAttribute("playsinline", true); 
    video.play();
    tick_roop = setInterval(tick,100);
    startBtn.style.display = 'none';
    stopBtn.style.display = 'block';
    cameraLoading.innerText = "⌛ 読み込み中...";
    })
    .catch(err => {
    cameraLoading.innerText = 'カメラにアクセスできません。カメラへのアクセスを許可してください。';
    startBtn.style.display = 'block';
    stopBtn.style.display = 'none';
    });
}

//カメラ再開
function stopCamera() {
    video.srcObject.getTracks()[0].stop();
    canvasElement.style.filter = 'blur(15px)'; 
    clearInterval(tick_roop);
    output.innerText = 'カメラ停止中';
    startBtn.style.display = 'block';
    stopBtn.style.display = 'none';
}

//カメラ再開
function startCamera() {
    navi();
    canvasElement.style.filter = 'blur(0px)'; 
    startBtn.style.display = 'none';
    stopBtn.style.display = 'block';
}

function tick() {
    if (video.readyState === video.HAVE_ENOUGH_DATA) {
    cameraLoading.hidden = true;
    canvasElement.hidden = false;
    outputContainer.hidden = false;

    canvasElement.height = video.videoHeight;
    canvasElement.width = video.videoWidth;
    canvas.drawImage(video, 0, 0, canvasElement.width, canvasElement.height);
    var imageData = canvas.getImageData(0, 0, canvasElement.width, canvasElement.height);
    var code = jsQR(imageData.data, imageData.width, imageData.height, {inversionAttempts: "dontInvert",});
    if (isReady&&code) {
        drawLine(code.location.topLeftCorner, code.location.topRightCorner, "#FF3B58");
        drawLine(code.location.topRightCorner, code.location.bottomRightCorner, "#FF3B58");
        drawLine(code.location.bottomRightCorner, code.location.bottomLeftCorner, "#FF3B58");
        drawLine(code.location.bottomLeftCorner, code.location.topLeftCorner, "#FF3B58");

        output.innerText = code.data;
        if(/\d{4}/.test(code.data) && isReady){
        isReady = false;
        sendData.kuninum = code.data;
        if(code.data!=preNum){
            sendFun();
        }else{
            isReady = true;
        }
        preNum = code.data;
        }
    } else {
        output.innerText = 'No data detected';
    }
    }
}

//QRコードモード
function qrChange(){
    manual.hidden = true;
    QRcode.hidden = false;
    isReady = true;
    startCamera();
    }

//手動モード
function manChange(){
    manual.hidden = false;
    QRcode.hidden = true;
    isReady = false;
    stopCamera();
}

function drawLine(begin, end, color) {
    canvas.beginPath();
    canvas.moveTo(begin.x, begin.y);
    canvas.lineTo(end.x, end.y);
    canvas.lineWidth = 4;
    canvas.strokeStyle = color;
    canvas.stroke();
}

//手動入力の送信時
function prep(){
    valueNum = inputNum.value;
    if((valueNum/1000) >=1 && (valueNum/1000) <4 && (valueNum%1000) >=100 && (valueNum%1000) <900 && (valueNum%100) >=1 && (valueNum%100) <= 42){
    notNumMsg.hidden = true;
    sendData.kuninum = valueNum;
    console.log(sendData);
    sendFun();
    isReady = false;
    }else{
    inputNum.value = '';
    notNumMsg.hidden = false;
    }
}

//送信
const sendFun = async () => {
    console.log(sendData);
    try{
    let data = await fetch('/staff/TOI_S', {
        method: "POST",
        headers: {
        "Content-Type": "application/json"
        },
        body: JSON.stringify(sendData)
    })
    .then(response => response.json());
    console.log(data);
    if(data.success){
        alert(`受付処理が完了しました。\n４桁番号：${sendData.kuninum}`)
    } else if (data.error === "Server Error") {
        alert("サーバーでエラーが発生しました。もう一度お願いします。");
    } else if(data.error === "Invalid JSON") {
        alert("データの送信形式が違います。何度もこの表示が出る場合は担当者にお知らせください。");
    } else if(data.error === "Invalid Kuninum") {
        alert("学籍番号の形式が違います。もう一度確認してください。");
    }else{
        alert("予期しないエラーです。")
    }
    } catch (err){
    alert("サーバーとの通信に失敗しました。再度試してください");
    }
    reset();
}

//リセット
function reset(){
    inputNum.value = '';
    isReady = true;
    outputData.parentElement.hidden = true;
    noData.hidden = false;
}
</script>
<style>
    body{
        width: 90vw;
        max-width: 900px;
        margin: 30px auto;
    }
    h1 {
        margin: 10px 0;
        font-size: 40px;
        text-align: center;
    }
    
    /*------------------------------------------------------------*/
    /* QRコード関連 */
    #QRcode{
        width: 85vw;
        max-width: 800px;
        margin: auto;
        margin-top: 50px;
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
    #stopBtn{display: none;}
    #startBtn{display: block;}
    
    #cameraLoading {
        text-align: center;
        padding: 40px;
        background-color: #eee;
    }
    
    #canvas {
        width: 100%;
    }
    
    #output {
        margin-top: 20px;
        background: #eee;
        padding: 10px;
        word-wrap: break-word;
        padding-bottom: 10px;
    }
    /*------------------------------------------------------------*/
    
    
    /*------------------------------------------------------------*/
    /* 手動入力関連 */
    #manual{
        height: 250px;
        width: 85vw;
        max-width: 800px;
        margin: 50px auto auto;
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
    
    #notNumMsg{
        color: red;
        margin: 30px;
    }
    
    #preBtn{
        display: block;
        color: #0029dd;
        font-size: 20px;
        border: 0;
        outline: 0;
        padding: 5px 10px;
        margin-top: 20px;
        margin-left: auto;
        background-color: #fff;
    }
    /*------------------------------------------------------------*/
    
    
    /*モード切り替えボタン*/
    .chBtn{
        display: block;
        color: #000;
        font-size: 15px;
        border: none;
        outline: none;
        padding: 10px, 20px;
        margin-top: 30px;
        margin-left: auto;
        background-color: #fff;
    }
    </style-->