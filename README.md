# Bandichtieng
<!DOCTYPE html>
<html>
<head>

<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>Care Mạnh Khanh</title>

<link rel="manifest" href="manifest.json">

<script src="https://cdn.jsdelivr.net/npm/tesseract.js@4/dist/tesseract.min.js"></script>

<style>

body{
font-family:Arial;
background:#f1f3f4;
margin:0;
padding:15px;
}

.container{
max-width:700px;
margin:auto;
background:white;
padding:20px;
border-radius:12px;
box-shadow:0 4px 20px rgba(0,0,0,0.15);
}

textarea{
width:100%;
height:110px;
padding:10px;
font-size:16px;
border-radius:8px;
border:1px solid #ddd;
}

.row{
display:flex;
gap:10px;
}

select{
flex:1;
padding:10px;
}

button{
margin-top:8px;
padding:10px;
width:100%;
border:none;
border-radius:8px;
background:#4285f4;
color:white;
font-size:16px;
}

.result{
margin-top:15px;
background:#fafafa;
padding:10px;
border-radius:8px;
min-height:60px;
}

video{
width:100%;
margin-top:10px;
border-radius:10px;
}

canvas{
display:none;
}

</style>

</head>

<body>

<div class="container">

<h2>AI Translator Lens</h2>

<div class="row">

<select id="from">
<option value="vi">Vietnamese</option>
<option value="en">English</option>
<option value="zh-CN">Chinese</option>
</select>

<select id="to">
<option value="zh-CN">Chinese</option>
<option value="en">English</option>
<option value="vi">Vietnamese</option>
</select>

</div>

<textarea id="inputText" placeholder="Enter text"></textarea>

<button onclick="translateText()">Translate</button>

<button onclick="voiceInput()">🎤 Voice Translate</button>

<input type="file" id="imageInput">

<button onclick="translateImage()">🖼 Translate Image</button>

<button onclick="startCamera()">📷 Camera OCR</button>

<video id="camera" autoplay></video>

<canvas id="canvas"></canvas>

<div id="result" class="result"></div>

</div>

<script>

async function translateText(){

let text=document.getElementById("inputText").value
let from=document.getElementById("from").value
let to=document.getElementById("to").value

let url=`https://translate.googleapis.com/translate_a/single?client=gtx&sl=${from}&tl=${to}&dt=t&q=${encodeURIComponent(text)}`

let res=await fetch(url)
let data=await res.json()

document.getElementById("result").innerText=data[0][0][0]

}

function voiceInput(){

let recognition=new(window.SpeechRecognition||window.webkitSpeechRecognition)()

recognition.lang="vi-VN"

recognition.start()

recognition.onresult=function(event){

let text=event.results[0][0].transcript

document.getElementById("inputText").value=text

translateText()

}

}

async function translateImage(){

let file=document.getElementById("imageInput").files[0]

const { data:{text} } = await Tesseract.recognize(file,'chi_sim')

document.getElementById("inputText").value=text

translateText()

}

async function startCamera(){

const video=document.getElementById("camera")

const stream=await navigator.mediaDevices.getUserMedia({video:true})

video.srcObject=stream

setInterval(captureFrame,3000)

}

async function captureFrame(){

const video=document.getElementById("camera")
const canvas=document.getElementById("canvas")

canvas.width=video.videoWidth
canvas.height=video.videoHeight

const ctx=canvas.getContext("2d")

ctx.drawImage(video,0,0)

const { data:{text} } = await Tesseract.recognize(canvas,'chi_sim')

if(text.trim().length>3){

document.getElementById("inputText").value=text

translateText()

}

}

</script>

</body>
</html>
