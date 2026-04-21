<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Diabetes Care App - Auth</title>

<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">

<style>
* { margin:0; padding:0; box-sizing:border-box; }
body { font-family: 'Roboto', Arial, sans-serif; }

/* HERO */
.hero {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
  display:flex;
  align-items:center;
  justify-content:center;
}

.hero-slide {
  position:absolute;
  width:100%; height:100%;
  background-size:cover;
  background-position:center;
  opacity:0;
  animation: fadeSlide 15s infinite;
}
.hero-slide:nth-child(1){animation-delay:0s;}
.hero-slide:nth-child(2){animation-delay:5s;}
.hero-slide:nth-child(3){animation-delay:10s;}
.hero-slide:nth-child(4){animation-delay:15s;}

@keyframes fadeSlide {
  0%{opacity:0;} 10%{opacity:1;} 33%{opacity:1;} 43%{opacity:0;} 100%{opacity:0;}
}

.hero::after {
  content:'';
  position:absolute;
  width:100%; height:100%;
  background:rgba(0,0,0,0.35);
}

/* AUTH BOX */
.auth-box {
  position:relative;
  z-index:3;
  background:white;
  padding:25px;
  border-radius:15px;
  width:340px;
  text-align:center;
  box-shadow:0 10px 30px rgba(0,0,0,0.3);
  max-height:90vh;
  overflow:auto;
}

h2 { margin-bottom:10px; }

input, select{
  width:100%;
  padding:9px;
  margin:6px 0;
  border-radius:8px;
  border:1px solid #ccc;
}

button{
  width:100%;
  padding:10px;
  background:#ffe7eb;
  border:none;
  border-radius:10px;
  font-weight:bold;
  cursor:pointer;
  transition:0.3s;
}

button:hover{
  background:#ffd4da;
  transform:translateY(-2px);
}

.toggle{
  margin-top:10px;
  cursor:pointer;
  color:#555;
  font-size:14px;
}

.error{
  color:red;
  font-size:13px;
  margin-top:5px;
}

.small{
  font-size:12px;
  color:#444;
  margin-top:4px;
  font-weight:bold;
}
</style>
</head>

<body>

<div class="hero">
  <div class="hero-slide" style="background-image:url('62c49b8d-ce40-4a70-b24e-45fa72382598.jpg');"></div>
  <div class="hero-slide" style="background-image:url('3e4dc9ab-9a43-48cf-9001-44b78667f65a.jpg');"></div>
  <div class="hero-slide" style="background-image:url('2b535cf8-0a90-4109-91a9-923e7e452b08.jpg');"></div>
  <div class="hero-slide" style="background-image:url('02b3abce-4b1f-42c5-9a0b-e9b6752efdff.jpg');"></div>

  <div class="auth-box">
    <h2 id="title">Login</h2>

    <input type="text" id="username" placeholder="Username">
    <input type="password" id="password" placeholder="Password">

    <!-- EXTRA FIELDS -->
    <div id="extraFields" style="display:none;">
      <input type="number" id="age" placeholder="Age">
      <input type="number" id="height" placeholder="Height (cm)">
      <input type="number" id="weight" placeholder="Weight (kg)">

      <div class="small">BMI: <span id="bmiValue">-</span></div>
      <div class="small">Status: <span id="bmiStatus">-</span></div>

      <select id="diabetesType">
        <option value="">Type of Diabetes</option>
        <option>Type 1</option>
        <option>Type 2</option>
        <option>Gestational</option>
        <option>Pre-diabetes</option>
      </select>

      <select id="lifestyle">
        <option value="">Lifestyle</option>
        <option>Sedentary</option>
        <option>Lightly Active</option>
        <option>Moderately Active</option>
        <option>Very Active</option>
      </select>

      <input type="password" id="confirmPassword" placeholder="Confirm Password">
    </div>

    <div class="error" id="error"></div>

    <button onclick="handleAuth()" id="btn">Login</button>

    <div class="toggle" onclick="toggleMode()" id="toggleText">
      Don't have an account? Sign up
    </div>
  </div>
</div>

<script>
let isLogin = true;

function toggleMode(){
  isLogin = !isLogin;

  document.getElementById("title").innerText = isLogin ? "Login" : "Sign Up";
  document.getElementById("btn").innerText = isLogin ? "Login" : "Create Account";
  document.getElementById("extraFields").style.display = isLogin ? "none" : "block";

  document.getElementById("toggleText").innerText = isLogin 
    ? "Don't have an account? Sign up" 
    : "Already have an account? Login";

  document.getElementById("error").innerHTML = "";
}

/* BMI + CLASSIFICATION */
function calcBMI(){
  let h = document.getElementById("height").value;
  let w = document.getElementById("weight").value;

  if(h && w){
    let bmi = (w / ((h/100) * (h/100))).toFixed(1);
    document.getElementById("bmiValue").innerText = bmi;

    let status = "";

    if(bmi < 18.5){
      status = "Underweight";
    } else if(bmi < 25){
      status = "Normal weight";
    } else if(bmi < 30){
      status = "Overweight";
    } else {
      status = "Obese";
    }

    document.getElementById("bmiStatus").innerText = status;

    return bmi;
  }

  return null;
}

document.addEventListener("input", function(e){
  if(e.target.id === "height" || e.target.id === "weight"){
    calcBMI();
  }
});

function handleAuth(){
  let user = document.getElementById("username").value;
  let pass = document.getElementById("password").value;
  let error = document.getElementById("error");

  if(user === "" || pass === ""){
    error.innerHTML = "Please fill username and password!";
    return;
  }

  if(isLogin){
    let savedUser = localStorage.getItem("username");
    let savedPass = localStorage.getItem("password");

    if(user === savedUser && pass === savedPass){
      localStorage.setItem("loggedInUser", user);
      window.location.href = "home.html";
    } else {
      error.innerHTML = "Invalid login!";
    }
  } else {
    let age = document.getElementById("age").value;
    let height = document.getElementById("height").value;
    let weight = document.getElementById("weight").value;
    let diabetes = document.getElementById("diabetesType").value;
    let lifestyle = document.getElementById("lifestyle").value;
    let confirm = document.getElementById("confirmPassword").value;

    if(!age || !height || !weight || !diabetes || !lifestyle){
      error.innerHTML = "Complete all health information!";
      return;
    }

    let bmi = calcBMI();

    if(pass.length < 6){
      error.innerHTML = "Password must be at least 6 characters!";
      return;
    }

    if(pass !== confirm){
      error.innerHTML = "Passwords do not match!";
      return;
    }

    localStorage.setItem("username", user);
    localStorage.setItem("password", pass);
    localStorage.setItem("age", age);
    localStorage.setItem("height", height);
    localStorage.setItem("weight", weight);
    localStorage.setItem("bmi", bmi);
    localStorage.setItem("diabetesType", diabetes);
    localStorage.setItem("lifestyle", lifestyle);

    alert("Account created successfully!");
    toggleMode();
  }
}
</script>

</body>
</html>
