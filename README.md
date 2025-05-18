<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Mectic Chat</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
  <script>
    emailjs.init("_rlJoYj24QiGJ2Via");

    let generatedOTP = "";

    function generateOTP() {
      return Math.floor(100000 + Math.random() * 900000).toString();
    }

    function showSection(id) {
      document.getElementById("login-section").style.display = "none";
      document.getElementById("otp-section").style.display = "none";
      document.getElementById("home-section").style.display = "none";
      document.getElementById("success-animation").style.display = "none";
      document.getElementById(id).style.display = "block";
    }

    function sendOTP(event) {
      event.preventDefault();

      const username = document.getElementById("username").value.trim();
      const email = document.getElementById("email").value.trim();
      const password = document.getElementById("password").value.trim();

      if (!username || !email || !password) {
        document.getElementById("login-message").innerText = "❌ Please fill all fields.";
        return;
      }

      generatedOTP = generateOTP();
      localStorage.setItem("otp", generatedOTP);
      localStorage.setItem("username", username);

      document.getElementById("login-message").innerText = "⏳ Sending OTP...";

      emailjs.send("service_mkrr7xo", "template_jfct2kp", {
        to_email: email,
        otp_code: generatedOTP,
        username: username
      }).then(() => {
        document.getElementById("login-message").innerText = "✅ OTP sent to " + email;
        setTimeout(() => {
          showSection("otp-section");
        }, 1500);
      }, (error) => {
        document.getElementById("login-message").innerText = "❌ Error sending OTP: " + error.text;
      });
    }

    function verifyOTP(event) {
      event.preventDefault();

      const inputOTP = document.getElementById("otp").value.trim();
      const realOTP = localStorage.getItem("otp");

      if (inputOTP === realOTP) {
        document.getElementById("otp-message").innerText = "✅ OTP Verified!";
        localStorage.setItem("lastLogin", new Date().getTime());

        showSection("success-animation");

        setTimeout(() => {
          showSection("home-section");
        }, 3000);
      } else {
        document.getElementById("otp-message").innerText = "❌ Incorrect OTP.";
      }
    }

    function checkLoginStatus() {
      const lastLogin = localStorage.getItem("lastLogin");
      if (lastLogin && new Date().getTime() - lastLogin < 5 * 60 * 1000) {
        showSection("home-section");
      } else {
        showSection("login-section");
      }
    }

    function openCamera() {
      const video = document.createElement("video");
      video.setAttribute("autoplay", "");
      video.setAttribute("playsinline", "");
      video.style.width = "100%";
      video.style.height = "300px";
      document.querySelector(".home-section-content").appendChild(video);

      navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } })
        .then(stream => video.srcObject = stream)
        .catch(err => alert("Camera access failed: " + err));
    }

    window.onload = checkLoginStatus;
  </script>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      background-color: #000;
      color: #fff;
    }

    .container {
      width: 300px;
      margin: 80px auto;
      padding: 20px;
      border-radius: 20px;
      background-color: #111;
      box-shadow: 0 0 15px rgba(255, 255, 255, 0.1);
    }

    input, button {
      width: 100%;
      padding: 15px;
      margin-bottom: 15px;
      border-radius: 20px;
      border: none;
      font-size: 16px;
    }

    input {
      background: #222;
      color: #fff;
    }

    button {
      background-color: #7E57C2;
      color: white;
      cursor: pointer;
    }

    h1 {
      text-align: center;
      color: #fff;
    }

    p {
      text-align: center;
    }

    .top-bar {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 60px;
      background-color: #7E57C2;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 15px;
      z-index: 1000;
    }

    .profile-icon,
    .settings-icon {
      width: 35px;
      height: 35px;
      border-radius: 50%;
      background-color: #fff;
      color: #7E57C2;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 18px;
      cursor: pointer;
    }

    .home-section-content {
      padding-top: 80px;
      text-align: center;
    }

    .bottom-nav {
      position: fixed;
      bottom: 0;
      left: 0;
      right: 0;
      background-color: #111;
      padding: 10px;
      border-top: 1px solid #444;
      display: flex;
      justify-content: space-around;
    }

    .bottom-nav button {
      width: 30%;
      padding: 10px;
      border: none;
      border-radius: 5px;
      background-color: #7E57C2;
      color: #fff;
      cursor: pointer;
    }

    /* Success Animation */
    #success-animation {
      display: none;
      height: 100vh;
      background: #000;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
    }

    .circle {
      width: 100px;
      height: 100px;
      border-radius: 50%;
      border: 4px solid #7E57C2;
      display: flex;
      align-items: center;
      justify-content: center;
      animation: pop 0.5s ease;
    }

    .checkmark {
      font-size: 50px;
      color: #7E57C2;
      animation: fadein 1s ease;
    }

    @keyframes pop {
      0% { transform: scale(0.5); }
      100% { transform: scale(1); }
    }

    @keyframes fadein {
      from { opacity: 0; transform: scale(0.8); }
      to { opacity: 1; transform: scale(1); }
    }
  </style>
</head>
<body>

  <!-- Login Section -->
  <div class="container" id="login-section">
    <h1>Login</h1>
    <form onsubmit="sendOTP(event)">
      <input type="text" id="username" placeholder="Username" required />
      <input type="email" id="email" placeholder="Email" required />
      <input type="password" id="password" placeholder="Password" required />
      <button type="submit">Submit</button>
    </form>
    <p id="login-message"></p>
  </div>

  <!-- OTP Section -->
  <div class="container" id="otp-section" style="display: none;">
    <h1>Enter OTP</h1>
    <form onsubmit="verifyOTP(event)">
      <input type="text" id="otp" placeholder="Enter OTP" required />
      <button type="submit">Verify OTP</button>
    </form>
    <p id="otp-message"></p>
  </div>

  <!-- Success Animation -->
  <div id="success-animation">
    <div class="circle">
      <div class="checkmark">✓</div>
    </div>
    <p style="color: #7E57C2; font-size: 20px; margin-top: 10px;">Verification Successful</p>
  </div>

  <!-- Home Section -->
  <div id="home-section" style="display: none;">
    <div class="top-bar">
      <div class="profile-icon" title="Profile">👤</div>
      <div class="settings-icon" title="Settings">⋮</div>
    </div>
    <div class="home-section-content">
      <h1>Welcome to Mectic Chat!</h1>
      <p>This is your dashboard.</p>
    </div>
    <div class="bottom-nav">
      <button onclick="showSection('home-section')">Home</button>
      <button onclick="openCamera()">Camera</button>
      <button>Message</button>
    </div>
  </div>

</body>
</html>
