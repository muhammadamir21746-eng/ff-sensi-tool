<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FF Sensi Tool</title>
<style>
body{margin:0;font-family:Arial,sans-serif;background:#0a0a23;color:white;}
header{background:#111;display:flex;justify-content:space-between;align-items:center;padding:20px;position:sticky;top:0;z-index:100;}
header h1{color:#00f5ff;animation: glow 2s infinite alternate;}
nav a, nav button, #userDisplay{color:white;margin-left:15px;text-decoration:none;font-weight:600;cursor:pointer;background:none;border:none;font-size:14px;}
@keyframes glow{0%{text-shadow:0 0 5px #00f5ff;}100%{text-shadow:0 0 20px #00f5ff;}}
.hero{padding:100px 20px;text-align:center;background:linear-gradient(rgba(0,0,0,0.5),rgba(0,0,0,0.5)), url('https://i.imgur.com/B0h0AqE.jpg') center/cover no-repeat;animation: bannerAnim 5s infinite alternate;}
@keyframes bannerAnim{0%{background-position:center top;}100%{background-position:center bottom;}}
.hero h2{font-size:36px;margin-bottom:20px;text-shadow: 2px 2px 8px #00f5ff;}
.section{padding:50px 20px;text-align:center;}
form{max-width:500px;margin:auto;display:flex;flex-direction:column;gap:15px;}
select,input{padding:10px;border-radius:6px;border:none;font-size:16px;}
button{padding:12px;background:#00f5ff;border:none;color:black;font-weight:700;cursor:pointer;border-radius:5px;transition:0.3s;}
button:hover{background:#00ffc2;transform:scale(1.05);}
.result{margin-top:20px;font-size:18px;font-weight:600;color:#00ffde;text-align:left;}
table{margin:auto;border-collapse:collapse;margin-top:20px;width:90%;max-width:600px;transition:0.3s;}
th,td{padding:10px;border:1px solid #00f5ff;text-align:center;transition:0.3s;}
tr:hover{background:#111827;}
.footer{background:#111;padding:20px;text-align:center;font-size:14px;color:#aaa;}
#loginBox{display:none;margin-bottom:20px;}
</style>
</head>
<body>

<header>
<h1>🎮 FF Sensi Tool</h1>
<nav>
<span id="userDisplay">Guest</span>
<button onclick="showLogin()">Login</button>
<button onclick="logout()">Logout</button>
</nav>
</header>

<section class="hero">
<h2>Calculate Your Free Fire Sensitivity</h2>
</section>

<div id="loginBox" class="section">
<input type="text" id="username" placeholder="Enter your username">
<button onclick="login()">Login</button>
</div>

<section id="calculator" class="section">
<form id="sensiForm" onsubmit="calculateSensi(event)">
<select id="device">
<option value="default">Select Device Type</option>
<option value="android">Android</option>
<option value="ios">iOS</option>
</select>
<input type="text" id="phoneName" placeholder="Enter your phone name or model" required>
<input type="number" id="generalInput" placeholder="Enter General Sensitivity" required>
<button type="submit">Calculate & Save</button>
</form>
<div class="result" id="sensiResult"></div>
</section>

<section id="history" class="section">
<h3>History</h3>
<div id="historyDisplay"></div>
</section>

<div class="footer">
© 2026 FF Sensi Tool
</div>

<script>
let currentUser = localStorage.getItem("user") || null;

function updateUI(){
  document.getElementById("userDisplay").innerText = currentUser? currentUser : "Guest";
  document.getElementById("loginBox").style.display = currentUser? "none":"block";
  displayHistory();
}
function showLogin(){document.getElementById("loginBox").style.display="block";}
function login(){let name=document.getElementById("username").value.trim(); if(name){currentUser=name;localStorage.setItem("user",name);updateUI();}}
function logout(){currentUser=null; localStorage.removeItem("user"); updateUI();}
function formatSensi(value){let num=parseFloat(value); return num%1===0? num:num.toFixed(1);}
function calculateSensi(e){
  e.preventDefault(); if(!currentUser){alert("Please login first!"); return;}
  const device=document.getElementById("device").value;
  const phone=document.getElementById("phoneName").value.trim();
  const general=parseFloat(document.getElementById("generalInput").value);
  if(device==="default"){alert("Please select device type"); return;}
  if(!phone){alert("Please enter phone name"); return;}
  let multiplier=device==="android"?1:0.9;
  let g=general*multiplier;
  let results={"General":formatSensi(g),"ADS":formatSensi(g*0.9),"Red Dot":formatSensi(g*0.8),"2x Scope":formatSensi(g*0.7),"4x Scope":formatSensi(g*0.6),"Sniper Scope":formatSensi(g*0.5)};
  let table=`<h4>${phone} (${device})</h4><table><tr><th>Scope</th><th>Sensitivity</th></tr>`;
  for(let key in results){table+=`<tr><td>${key}</td><td>${results[key]}</td></tr>`;}
  table+="</table>";
  document.getElementById("sensiResult").innerHTML=table;
  let history=JSON.parse(localStorage.getItem("history_"+currentUser) || "[]");
  history.unshift({phone,device,results});
  localStorage.setItem("history_"+currentUser,JSON.stringify(history));
  displayHistory();
}
function displayHistory(){
  if(!currentUser){document.getElementById("historyDisplay").innerHTML="Please login to see history."; return;}
  let history=JSON.parse(localStorage.getItem("history_"+currentUser) || "[]");
  if(history.length===0){document.getElementById("historyDisplay").innerHTML="No history yet."; return;}
  let html="";
  history.forEach(item=>{
    html+=`<h4>${item.phone} (${item.device})</h4><table><tr><th>Scope</th><th>Sensitivity</th></tr>`;
    for(let key in item.results){html+=`<tr><td>${key}</td><td>${item.results[key]}</td></tr>`;}
    html+="</table><br>";
  });
  document.getElementById("historyDisplay").innerHTML=html;
}
updateUI();
</script>

</body>
</html>
