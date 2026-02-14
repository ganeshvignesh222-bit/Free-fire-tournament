# Free-fire-tournament


<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>WAXX 3.0</title>
<style>
body{
    margin:0;
    font-family: Arial, sans-serif;
    background: linear-gradient(135deg,#0f2027,#203a43,#2c5364);
    height:100vh;
    display:flex;
    justify-content:center;
    align-items:center;
    color:white;
}
.app{
    width:95%;
    max-width:650px;
    height:92vh;
    background:rgba(0,0,0,0.65);
    border-radius:20px;
    display:flex;
    flex-direction:column;
}
.header{
    padding:18px;
    text-align:center;
    border-bottom:1px solid rgba(255,255,255,0.1);
}
.header h1{margin:0;letter-spacing:3px;}
.version{font-size:12px;color:#9fdcff;}
.chat{
    flex:1;
    padding:18px;
    overflow-y:auto;
}
.msg{
    margin-bottom:12px;
    max-width:80%;
    padding:12px;
    border-radius:12px;
    font-size:14px;
}
.user{background:#00e5ff;color:black;margin-left:auto;}
.bot{background:rgba(255,255,255,0.12);color:#c9f6ff;}
.controls{
    display:flex;
    gap:8px;
    padding:12px;
    border-top:1px solid rgba(255,255,255,0.1);
}
input{
    flex:1;
    padding:12px;
    border:none;
    border-radius:8px;
    font-size:15px;
}
button{
    padding:12px 16px;
    border:none;
    border-radius:8px;
    background:#00e5ff;
    font-weight:bold;
    cursor:pointer;
}
.footer{
    text-align:center;
    font-size:11px;
    color:#ccc;
    padding-bottom:10px;
}
.profile{
    font-size:12px;
    color:#bdefff;
}
</style>
</head>
<body>

<div class="app">
    <div class="header">
        <h1>WAXX</h1>
        <div class="version">Mini AI Agent • Version 3.0</div>
        <div class="profile" id="profile"></div>
    </div>

    <div class="chat" id="chat"></div>

    <div class="controls">
        <input id="input" placeholder="Ask WAXX anything...">
        <button onclick="send()">Send</button>
        <button onclick="voice()">🎤</button>
    </div>

    <div class="footer">Created by <b>Sai Ganesh</b></div>
</div>

<script>
// ===== MEMORY SYSTEM =====
let memory = JSON.parse(localStorage.getItem("waxx_v3")) || {};
function save(){ localStorage.setItem("waxx_v3", JSON.stringify(memory)); }

const chat = document.getElementById("chat");
const profile = document.getElementById("profile");

function updateProfile(){
    profile.innerText = memory.name ? "User: "+memory.name : "User: Guest";
}
updateProfile();

function addMsg(text,type){
    const d=document.createElement("div");
    d.className="msg "+type;
    d.innerText=text;
    chat.appendChild(d);
    chat.scrollTop=chat.scrollHeight;
}

// ===== MINI AI CORE =====
function aiGenerate(q){
    q=q.toLowerCase();

    if(q.includes("my name is")){
        memory.name=q.split("my name is")[1].trim();
        save(); updateProfile();
        return "Got it. I saved your name.";
    }

    if(q.includes("what is my name")){
        return memory.name ? "Your name is "+memory.name+"." : "I don't know your name yet.";
    }

    let words=q.split(" ").filter(w=>w.length>3);
    let key=words[Math.floor(Math.random()*words.length)] || "this topic";

    const patterns={
        why:[
            "This happens due to logical reasons related to "+key+".",
            "The cause is connected with how "+key+" works."
        ],
        how:[
            "It works step by step using simple logic around "+key+".",
            "The process involves basic actions with "+key+"."
        ],
        what:[
            key+" is a concept explained in a simple way.",
            "It refers to something connected with "+key+"."
        ]
    };

    if(q.includes("why")) return pick(patterns.why);
    if(q.includes("how")) return pick(patterns.how);
    if(q.includes("what")||q.includes("explain")) return pick(patterns.what);
    if(q.includes("ai")) return "I am a mini AI agent that generates answers.";
    if(q.includes("your name")) return "I am WAXX, version 3.0.";
    if(q.includes("created")) return "I was created by Sai Ganesh.";

    return "I analyzed your question and responded briefly.";
}

function pick(arr){ return arr[Math.floor(Math.random()*arr.length)]; }

// ===== TYPING EFFECT =====
function type(text){
    let i=0;
    let d=document.createElement("div");
    d.className="msg bot";
    chat.appendChild(d);
    let t=setInterval(()=>{
        d.innerText+=text.charAt(i++);
        chat.scrollTop=chat.scrollHeight;
        if(i>=text.length) clearInterval(t);
    },25);
}

// ===== SEND =====
function send(){
    const inp=document.getElementById("input");
    if(inp.value.trim()=="")return;
    addMsg(inp.value,"user");
    let q=inp.value;
    inp.value="";
    setTimeout(()=>type(aiGenerate(q)),400);
}

// ===== VOICE INPUT =====
function voice(){
    if(!('webkitSpeechRecognition' in window)){
        alert("Voice not supported");
        return;
    }
    let rec=new webkitSpeechRecognition();
    rec.lang="en-US";
    rec.start();
    rec.onresult=e=>{
        document.getElementById("input").value=e.results[0][0].transcript;
    };
}
</script>

</body>
</html>