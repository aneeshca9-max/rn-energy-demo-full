<!DOCTYPE html>
<html lang="ml">
<head>
  <meta charset="UTF-8">
  <title>RN Energy - Water Level Simulation</title>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: #0a2a43;
      color: #fff;
      text-align: center;
    }

    .tank {
      width: 120px;
      height: 300px;
      border: 3px solid #fff;
      margin: 20px;
      position: relative;
      display: inline-block;
      border-radius: 6px;
    }

    .fill {
      position: absolute;
      bottom: 0;
      width: 100%;
      background: #00bfff;
      height: 0%;
      transition: height 0.5s;
    }

    .level {
      position: absolute;
      top: 10px;
      width: 100%;
      font-weight: bold;
    }

    .label {
      margin-top: 10px;
      font-size: 16px;
    }

    .status {
      margin-top: 20px;
    }

    .dot {
      width: 15px;
      height: 15px;
      border-radius: 50%;
      display: none;
      margin: 5px auto;
    }

    .green { background: lime; }
    .red { background: red; }
  </style>
</head>

<body>

  <h2>RN Energy – Fast Demo Simulation</h2>

  <div id="borewell" class="tank">
    <div class="fill"></div>
    <div class="level"></div>
  </div>
  <div class="label">Borewell</div>

  <div id="filter" class="tank">
    <div class="fill"></div>
    <div class="level"></div>
  </div>
  <div class="label">Filter Tank</div>

  <div class="status">
    <div class="dot green"></div>
    <div>Motor ON</div>

    <div class="dot red"></div>
    <div>Dry Run</div>
  </div>

<script>
// ===============================
// RN ENERGY - FAST DEMO SIMULATION
// ===============================

let motorState = false;
let mode = "AUTO";
let borewellLevel = 80;
let filterTankLevel = 20;
let dryRun = false;

const SPEED_INTERVAL = 1000;
const FILL_RATE = 8;
const DRAIN_RATE = 6;

function updateUI() {
  document.querySelector("#borewell .fill").style.height = borewellLevel + "%";
  document.querySelector("#borewell .level").innerText = borewellLevel + "%";

  document.querySelector("#filter .fill").style.height = filterTankLevel + "%";
  document.querySelector("#filter .level").innerText = filterTankLevel + "%";

  document.querySelector(".dot.green").style.display = motorState ? "block" : "none";
  document.querySelector(".dot.red").style.display = dryRun ? "block" : "none";
}

function motorON() {
  if (!dryRun) motorState = true;
}
function motorOFF() {
  motorState = false;
}

setInterval(() => {

  if (motorState) {
    filterTankLevel += FILL_RATE;
    borewellLevel -= DRAIN_RATE;
  }

  filterTankLevel = Math.min(filterTankLevel, 100);
  borewellLevel = Math.max(borewellLevel, 0);

  if (borewellLevel <= 10 && motorState) {
    dryRun = true;
    motorOFF();
  }

  if (mode === "AUTO") {
    if (filterTankLevel <= 30 && !dryRun) motorON();
    if (filterTankLevel >= 100) motorOFF();
  }

  if (filterTankLevel >= 100 && !motorState) {
    setTimeout(() => {
      filterTankLevel = 20;
      borewellLevel = 80;
      dryRun = false;
    }, 2000);
  }

  updateUI();

}, SPEED_INTERVAL);

updateUI();
</script>

</body>
</html>
