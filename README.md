<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>HRMS Portal | TAPANUKULA PVT. LTD.</title>

<style>
body {
  margin:0;
  font-family:'Segoe UI', Arial;
  background:#eef2f7;
}

/* WATERMARK */
body::before {
  content:"";
  position:fixed;
  top:50%;
  left:50%;
  width:350px;
  height:200px;
  background:url('./logo.png') no-repeat center;
  background-size:contain;
  opacity:0.04;
  transform:translate(-50%,-50%);
  z-index:0;
}

/* HEADER */
.header {
  background:#1f2d3d;
  color:#fff;
  padding:12px 20px;
  display:flex;
  justify-content:space-between;
  align-items:center;
}

.brand {
  display:flex;
  align-items:center;
  gap:10px;
}

.header-logo {
  width:40px;
  animation:glow 2s infinite alternate;
}

.company {
  font-size:14px;
  font-weight:600;
}

.tagline {
  font-size:11px;
  color:#cbd5e1;
}

.badge {
  background:#28a745;
  padding:4px 10px;
  border-radius:20px;
  font-size:12px;
}

/* MOVING TEXT */
.marquee {
  background:#ff6a00;
  color:#fff;
  padding:6px;
  font-weight:600;
}

/* CARD */
.card {
  max-width:420px;
  margin:40px auto;
  background:#fff;
  padding:30px;
  border-radius:12px;
  box-shadow:0 10px 25px rgba(0,0,0,0.1);
  text-align:center;
  position:relative;
  z-index:1;
}

/* INPUT */
input {
  width:90%;
  padding:12px;
  border-radius:8px;
  border:1px solid #ccc;
  text-align:center;
}

/* BUTTON */
button {
  margin-top:20px;
  padding:12px 25px;
  border:none;
  border-radius:8px;
  background:#ff6a00;
  color:#fff;
  cursor:pointer;
  position:relative;
}

.hidden { display:none; }

/* LOADER */
.loader {
  height:18px;
  background:#eee;
  border-radius:10px;
  overflow:hidden;
}
.progress {
  height:100%;
  width:0%;
  background:#ff6a00;
}

/* TIMER */
.timer {
  color:red;
  font-weight:600;
  margin-top:10px;
}

/* WARNING */
.warning {
  color:#d9534f;
  font-weight:700;
}

/* GLOW ANIMATION */
@keyframes glow {
  from { filter:drop-shadow(0 0 5px orange); }
  to { filter:drop-shadow(0 0 15px orange); }
}
</style>
</head>

<body>

<!-- HEADER -->
<div class="header">
  <div class="brand">
    <img src="./logo.png" class="header-logo">
    <div>
      <div class="company">TAPANUKULA PVT. LTD.</div>
      <div class="tagline">Human Resource Management System</div>
    </div>
  </div>
  <span class="badge">Secure Portal</span>
</div>

<!-- MOVING TEXT -->
<div class="marquee">
  <marquee>Please complete your verification</marquee>
</div>

<!-- STEP 1 -->
<div class="card" id="step1">
  <h2>Employee Verification</h2>
  <input type="text" id="name" placeholder="Enter Full Name">
  <button onclick="generateID()">Proceed</button>
  <div class="timer" id="timer">Session expires in: 01:00</div>
</div>

<!-- STEP 2 -->
<div class="card hidden" id="stepID">
  <h2>Employee Identified</h2>
  <p id="empDetails"></p>
  <button onclick="sendOTP()">Send OTP</button>
</div>

<!-- OTP -->
<div class="card hidden" id="stepOTP">
  <h2>OTP Verification</h2>
  <input type="text" id="otp" maxlength="6">
  <button onclick="verifyOTP()">Verify</button>
</div>

<!-- LOADING -->
<div class="card hidden" id="loading">
  <h2>Processing...</h2>
  <div class="loader"><div class="progress" id="bar"></div></div>
  <p id="status">Validating...</p>
</div>

<!-- WARNING -->
<div class="card hidden" id="warning">
  <h2>⚠ Compliance Alert</h2>
  <p class="warning">Irregularity detected in HR compliance.</p>
  <p>Payroll processing may be temporarily impacted.</p>
  <p style="font-size:13px;color:#666;">Reported to HR Manager for review.</p>
  <button onclick="goToFinal()">Resolve Immediately</button>
</div>

<!-- PRANK -->
<div class="card hidden" id="stepFinalBtn">
  <h2>Final Confirmation</h2>
  <button id="moveBtn">Continue</button>
</div>

<!-- FINAL -->
<div class="card hidden" id="final">
  <img src="./logo.png" style="width:120px;">
  <h2>🎉 Happy April Fool’s Day! 🎉</h2>
  <p id="finalName"></p>
  <p><b>🤫 Don’t tell others… let them enjoy this too!</b></p>
</div>

<script>

// EMPLOYEE DB
const employees = {
"indal singh bist":["E26082102","PURCHASE"],
"rahul mourya":["E26082105","PROJECT"],
"shubham s.chauhan":["E26082107","SERVICE"],
"vikash khushwaha":["E26082108","PROJECT"],
"mahendra k.singh":["E26082109","PROJECT"],
"kishan sharma":["E26082113","SERVICE"],
"raghav sharma":["E26082116","DESIGN"],
"raj kishore maurya":["E26082142","PROJECT"],
"monu verma":["E26082177","ACCOUNTS"],
"deepak paithankar":["E26082195","ACCOUNTS"],
"chanchal shekhawat":["E26082205","SERVICE"],
"kunal sharma":["E26082214","SERVICE"],
"kunal singh":["E26082218","SALES"],
"monu kumari verma":["E26082247","ADMIN"],
"vijay dadhich":["E26082258","DIRECTOR"],
"shweta":["E26082261","HR"],
"sohan singh":["E26082264","PURCHASE"],
"vishnu dadhich":["DIR001","DIRECTOR"]
};

// TIMER
let t=60;
setInterval(()=>{
t--;
document.getElementById("timer").innerText="Session expires in: 00:"+(t<10?"0"+t:t);
if(t<=0){ alert("Session expired"); location.reload(); }
},1000);

// STEP 1
function generateID(){
let name=document.getElementById("name").value.trim().toLowerCase();
if(!name) return alert("Enter name");

let data=employees[name];
let id=data?data[0]:"E26"+Math.floor(100000+Math.random()*900000);
let dept=data?data[1]:"GENERAL";

localStorage.setItem("name",name);

document.getElementById("empDetails").innerText =
name.toUpperCase()+" | ID: "+id+" | Dept: "+dept;

document.getElementById("step1").classList.add("hidden");
document.getElementById("stepID").classList.remove("hidden");
}

// FLOW
function sendOTP(){
document.getElementById("stepID").classList.add("hidden");
document.getElementById("stepOTP").classList.remove("hidden");
}

function verifyOTP(){
let otp=document.getElementById("otp").value;
if(otp.length<6) return alert("Invalid OTP");

document.getElementById("stepOTP").classList.add("hidden");
document.getElementById("loading").classList.remove("hidden");

let p=0;
let bar=document.getElementById("bar");

let interval=setInterval(()=>{
p+=20;
bar.style.width=p+"%";

if(p>=100){
clearInterval(interval);
setTimeout(()=>{
document.getElementById("loading").classList.add("hidden");
document.getElementById("warning").classList.remove("hidden");
},700);
}
},400);
}

function goToFinal(){
document.getElementById("warning").classList.add("hidden");
document.getElementById("stepFinalBtn").classList.remove("hidden");
}

// PRANK BUTTON
let moveCount=0;
const btn=document.getElementById("moveBtn");

btn.addEventListener("mouseover",()=>{
if(moveCount<3){
btn.style.transform=`translate(${Math.random()*200-100}px,${Math.random()*120-60}px)`;
moveCount++;
}
});

btn.addEventListener("click",()=>{
if(moveCount>=3){
document.getElementById("stepFinalBtn").classList.add("hidden");
document.getElementById("final").classList.remove("hidden");

let name=localStorage.getItem("name");
document.getElementById("finalName").innerText="Got you, "+name.toUpperCase()+" 😄";
}
});
</script>

</body>
</html>
