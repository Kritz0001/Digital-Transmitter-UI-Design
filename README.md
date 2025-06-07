# Digital-Transmitter-UI-Design
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Digital Transmitter UI</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }
    .gauge {
      width: 200px;
      height: 200px;
      border-radius: 50%;
      background: radial-gradient(circle, #1e293b 40%, #0f172a 100%);
      border: 6px solid #38bdf8;
      position: relative;
      box-shadow: 0 0 20px rgba(56, 189, 248, 0.4);
    }
    .tick {
      position: absolute;
      width: 4px;
      height: 12px;
      background: #7dd3fc;
      top: 10px;
      left: 50%;
      transform-origin: center 90px;
    }
    .gauge-box {
      background: #0f172a;
      padding: 3rem;
      border-radius: 1rem;
      box-shadow: 0 0 20px rgba(56, 189, 248, 0.3);
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 2rem;
      position: relative;
      width: 65%;
      max-width: 1000px;
    }
    .half-gauge {
      width: 200px;
      height: 100px;
      border-top-left-radius: 200px;
      border-top-right-radius: 200px;
      overflow: hidden;
      background: radial-gradient(circle at bottom, #1e293b 30%, #0f172a 100%);
      border: 6px solid #38bdf8;
      border-bottom: none;
      position: relative;
      box-shadow: 0 0 20px rgba(56, 189, 248, 0.4);
    }
    .half-tick {
      position: absolute;
      width: 4px;
      height: 12px;
      background: #7dd3fc;
      bottom: 0;
      left: 50%;
      transform-origin: center bottom;
    }
    .needle {
      width: 4px;
      height: 90px;
      background: red;
      position: absolute;
      top: 10px;
      left: 50%;
      transform-origin: bottom center;
      transform: rotate(0deg);
      transition: transform 0.5s ease-in-out;
    }
    .needle-half {
      height: 70px;
      top: 20px;
    }
    .needle-cap {
      width: 16px;
      height: 16px;
      background: white;
      border-radius: 50%;
      border: 2px solid #f87171;
      position: absolute;
      bottom: 20px;
      left: 50%;
      transform: translateX(-50%);
      z-index: 10;
    }
  </style>
</head>
<body class="bg-gray-900 text-white flex items-center justify-center min-h-screen flex-col gap-6">

  <h1 class="text-3xl font-bold text-sky-400">Digital Transmitter UI</h1>

  <div class="gauge-box relative">
    <div class="flex gap-20 justify-center">
      <!-- 360 Degree Gauge Box -->
      <div class="flex flex-col items-center">
        <div class="gauge" id="azimuth-gauge">
          <div class="needle" id="azimuth-needle"></div>
          <div class="needle-cap"></div>
        </div>
        <p class="mt-2 text-xl text-sky-300">Azimuth: <span id="azimuth-val">0</span>°</p>
      </div>

      <!-- 90 Degree Half Gauge Box -->
      <div class="flex flex-col items-center">
        <div class="half-gauge" id="elevation-gauge">
          <div class="needle needle-half" id="elevation-needle"></div>
          <div class="needle-cap"></div>
        </div>
        <p class="mt-2 text-xl text-sky-300">Elevation: <span id="elevation-val">0</span>°</p>
      </div>
    </div>

    <!-- Bottom Left Status -->
    <div class="absolute left-4 bottom-4 text-base text-sky-200 space-y-1">
      <p>Servo Status: <span class="font-semibold" id="servo-status">Idle</span></p>
      <p>Feedback: <span class="font-semibold" id="feedback">Matched ✔</span></p>
    </div>

    <!-- Bottom Right Toggle -->
    <div class="absolute right-4 bottom-4 flex items-center gap-2">
      <span class="text-xl">E</span>
      <label class="relative inline-flex items-center cursor-pointer">
        <input type="checkbox" id="mode-toggle" class="sr-only peer">
        <div class="w-14 h-7 bg-gray-600 rounded-full peer peer-checked:bg-green-500 transition duration-300"></div>
        <div class="absolute left-1 top-1 w-5 h-5 bg-white rounded-full transition-transform peer-checked:translate-x-7"></div>
      </label>
      <span class="text-xl">T</span>
    </div>
  </div>

  <script>
    const azimuthNeedle = document.getElementById("azimuth-needle");
    const elevationNeedle = document.getElementById("elevation-needle");
    const azimuthVal = document.getElementById("azimuth-val");
    const elevationVal = document.getElementById("elevation-val");
    const modeToggle = document.getElementById("mode-toggle");
    const servoStatus = document.getElementById("servo-status");
    const feedback = document.getElementById("feedback");
    const azimuthGauge = document.getElementById("azimuth-gauge");
    const elevationGauge = document.getElementById("elevation-gauge");

    for (let i = 0; i < 12; i++) {
      const tick = document.createElement("div");
      tick.className = "tick";
      tick.style.transform = `rotate(${i * 30}deg)`;
      azimuthGauge.appendChild(tick);
    }

    for (let i = 0; i <= 9; i++) {
      const tick = document.createElement("div");
      tick.className = "half-tick";
      tick.style.transform = `rotate(${-45 + i * 10}deg)`;
      elevationGauge.appendChild(tick);
    }

    let elevation = 0;

    function updateUI() {
      const azimuth = Math.floor(Math.random() * 360);
      if (modeToggle.checked) {
        azimuthNeedle.style.transform = `rotate(${azimuth}deg)`;
        azimuthVal.textContent = azimuth;
        servoStatus.textContent = "Moving";
        feedback.textContent = Math.random() > 0.5 ? "Matched ✔" : "Not Matched ✘";
      } else {
        elevation = Math.floor(Math.random() * 91);
        elevationNeedle.style.transform = `rotate(${elevation - 45}deg)`;
        elevationVal.textContent = elevation;
        servoStatus.textContent = "Idle";
        feedback.textContent = Math.random() > 0.5 ? "Matched ✔" : "Not Matched ✘";
      }
    }

    setInterval(updateUI, 2000);
  </script>

</body>
</html>

