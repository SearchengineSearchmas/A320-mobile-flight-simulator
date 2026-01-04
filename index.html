<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
<title>A320 Mobile Flight Sim — Tilt & Swipe</title>
<style>
  :root {
    --bg: #0b1622;
    --panel: #122235;
    --accent: #00d2ff;
    --text: #e6eef6;
    --warn: #ffce00;
    --good: #5efc8d;
    --skyTop: #3a89ff;
    --skyBottom: #7fc9ff;
    --groundTop: #4c9b55;
    --groundBottom: #2f6b3a;
  }
  html, body { margin:0; padding:0; background:var(--bg); color:var(--text); font-family:system-ui, -apple-system, Segoe UI, Roboto, sans-serif; }
  #app { height:100vh; display:flex; flex-direction:column; }
  header {
    background:var(--panel); border-bottom:1px solid #1c3b5c; padding:8px 12px; display:flex; gap:8px; align-items:center; justify-content:space-between;
  }
  header .title { font-weight:600; letter-spacing:0.3px; }
  #world { position:relative; flex:1; overflow:hidden; background:#0a1521; }
  canvas { position:absolute; inset:0; touch-action:none; }
  #hud {
    position:absolute; top:8px; left:8px; right:8px; display:flex; gap:8px; justify-content:space-between; pointer-events:none;
  }
  .tape {
    background:rgba(0,0,0,0.35); backdrop-filter: blur(2px); border:1px solid #2a4b72; border-radius:6px; padding:8px;
    min-width:92px;
  }
  .tape .label { font-size:12px; opacity:0.9; }
  .tape .value { font-size:18px; font-weight:700; }
  /* Throttle bar (right side) */
  #thrBar {
    position:absolute; right:8px; top:50%; transform:translateY(-50%);
    width:14px; height:55%; border:1px solid #2a4b72; border-radius:8px; overflow:hidden; background:#0e1c2c;
  }
  #thrFill { width:100%; height:50%; background:linear-gradient(#00e5ff, #0077ff); transition:height 120ms ease; }
  #thrLabel {
    position:absolute; right:28px; top:50%; transform:translateY(-50%); background:rgba(0,0,0,0.35);
    border:1px solid #2a4b72; border-radius:6px; padding:6px 8px; font-size:12px;
  }
  /* Engine toggle */
  #engineToggle {
    position:absolute; bottom:16px; left:16px; width:56px; height:56px; border-radius:50%;
    display:grid; place-items:center; background:#0e1c2c; border:1px solid #2a4b72; box-shadow:0 0 0 0 rgba(0,226,255,0.0);
    transition:box-shadow 200ms ease, background 200ms ease;
    touch-action:manipulation;
  }
  #engineToggle.on { box-shadow:0 0 18px 4px rgba(0,226,255,0.45), inset 0 0 12px rgba(0,226,255,0.3); background:#0f2c40; }
  #engineIcon {
    width:32px; height:32px;
    background: radial-gradient(circle at 50% 50%, #00e6ff 0%, #0077ff 60%, #004b8a 100%);
    border-radius:50%;
  }
  /* Sensor permission button (iOS) */
  #sensorBtn {
    position:absolute; bottom:16px; right:16px; padding:10px 12px; background:#142741; color:var(--text); border:1px solid #2a4b72; border-radius:6px;
  }
  #sensorBtn.hidden { display:none; }

  footer { padding:6px 10px; background:#0b1622; font-size:12px; opacity:0.85; text-align:center; }
</style>
</head>
<body>
<div id="app">
  <header>
    <div class="title">A320 Flight Sim — Tilt & Swipe</div>
    <div style="font-size:12px; opacity:0.8;">Tilt to fly • Swipe right side for throttle • Tap engine</div>
  </header>

  <section id="world">
    <canvas id="simCanvas"></canvas>

    <!-- HUD tapes -->
    <div id="hud">
      <div class="tape"><div class="label">IAS (kn)</div><div class="value" id="spdVal">000</div></div>
      <div class="tape"><div class="label">ALT (ft)</div><div class="value" id="altVal">0000</div></div>
      <div class="tape"><div class="label">HDG</div><div class="value" id="hdgVal">000</div></div>
    </div>

    <!-- Throttle bar and label -->
    <div id="thrBar"><div id="thrFill"></div></div>
    <div id="thrLabel">Throttle: <span id="thrPct">50%</span></div>

    <!-- Engine toggle -->
    <div id="engineToggle" class="on" title="Toggle Engine">
      <div id="engineIcon"></div>
    </div>

    <!-- Sensor permission (iOS) -->
    <button id="sensorBtn" class="hidden">Enable sensors</button>
  </section>

  <footer>Tilt: pitch/roll • Swipe (right side): throttle • Tap engine to toggle • Simple physics, tuned for mobile feel</footer>
</div>

<script>
/* --- Setup --- */
const canvas = document.getElementById('simCanvas');
const ctx = canvas.getContext('2d');
function resize() {
  canvas.width = canvas.clientWidth;
  canvas.height = canvas.clientHeight;
}
window.addEventListener('resize', resize);
resize();

/* --- UI refs --- */
const spdVal = document.getElementById('spdVal');
const altVal = document.getElementById('altVal');
const hdgVal = document.getElementById('hdgVal');
const thrFill = document.getElementById('thrFill');
const thrPct = document.getElementById('thrPct');
const engineToggle = document.getElementById('engineToggle');
const sensorBtn = document.getElementById('sensorBtn');

/* --- State --- */
const state = {
  pitch: 0,     // deg
  roll: 0,      // deg
  heading: 90,  // deg
  alt: 1200,    // m
  vs: 0,        // m/s
  tas: 90,      // m/s
  ias: 90,      // m/s approx
  throttle: 0.5, // 0..1
  engineOn: true,
};

/* --- Environment & constants --- */
const env = {
  dt: 0.016,
  g: 9.81,
  rho: 1.225,
  mass: 70000,
  wingArea: 122.6,
  clAlpha: 0.085,
  cd0: 0.022, cdI: 0.045,
  thrustMax: 2.5e5,
  bankTurnGain: 3.4,
  pitchSmooth: 0.15,
  rollSmooth: 0.15,
};

/* --- Sensor permission (iOS 13+) --- */
function setupSensors() {
  const needPerm = typeof DeviceMotionEvent !== 'undefined' && typeof DeviceMotionEvent.requestPermission === 'function';
  if (needPerm) {
    sensorBtn.classList.remove('hidden');
    sensorBtn.addEventListener('click', async () => {
      try {
        const res = await DeviceMotionEvent.requestPermission();
        if (res === 'granted') {
          sensorBtn.classList.add('hidden');
        }
      } catch (e) {}
    });
  }
}
setupSensors();

/* --- Tilt controls --- */
let targetPitchNorm = 0; // -1..+1
let targetRollNorm = 0;  // -1..+1

window.addEventListener('deviceorientation', (e) => {
  // beta: front/back tilt (~ -180..180), gamma: left/right (~ -90..90)
  const beta = e.beta ?? 0;  // pitch
  const gamma = e.gamma ?? 0; // roll
  // Normalize roughly: aim for -1..+1
  targetPitchNorm = Math.max(-1, Math.min(1, beta / 45));   // ~ +/-45° phone tilt
  targetRollNorm  = Math.max(-1, Math.min(1, gamma / 45));
}, true);

/* --- Swipe throttle (right half) --- */
let swipeActive = false, swipeLastY = 0;
canvas.addEventListener('touchstart', (e) => {
  const t = e.touches[0];
  if (t.clientX > window.innerWidth * 0.6) {
    swipeActive = true;
    swipeLastY = t.clientY;
  }
}, {passive:true});
canvas.addEventListener('touchmove', (e) => {
  if (!swipeActive) return;
  const t = e.touches[0];
  const dy = swipeLastY - t.clientY; // swipe up increases throttle
  const delta = dy / 300; // sensitivity
  state.throttle = Math.max(0, Math.min(1, state.throttle + delta));
  swipeLastY = t.clientY;
}, {passive:true});
canvas.addEventListener('touchend', () => { swipeActive = false; }, {passive:true});
canvas.addEventListener('touchcancel', () => { swipeActive = false; }, {passive:true});

/* --- Engine toggle --- */
engineToggle.addEventListener('click', () => {
  state.engineOn = !state.engineOn;
  engineToggle.classList.toggle('on', state.engineOn);
});

/* --- Physics --- */
function step(dt) {
  // Smooth tilt to aircraft attitude (deg)
  const targetPitchDeg = targetPitchNorm * 10; // limit commanded pitch
  const targetRollDeg  = targetRollNorm  * 25; // limit commanded bank
  state.pitch += (targetPitchDeg - state.pitch) * env.pitchSmooth;
  state.roll  += (targetRollDeg  - state.roll)  * env.rollSmooth;

  // Aerodynamics (simplified)
  const alpha = state.pitch; // deg proxy
  let cl = env.clAlpha * alpha + 0.3;
  const q = 0.5 * env.rho * state.tas * state.tas;
  const lift = q * env.wingArea * cl;
  const weight = env.mass * env.g;

  // Drag
  const cd = env.cd0 + env.cdI * cl * cl;
  const drag = q * env.wingArea * cd;

  // Thrust
  let thrust = state.engineOn ? env.thrustMax * state.throttle : 0;

  // Vertical dynamics
  const bankRad = state.roll * Math.PI/180;
  const liftVertical = lift * Math.cos(bankRad);
  const fz = liftVertical - weight;
  const az = fz / env.mass;
  state.vs += az * dt;
  state.alt += state.vs * dt;
  if (state.alt < 0) { state.alt = 0; state.vs = Math.max(0, state.vs); }

  // Forward dynamics
  const ax = (thrust - drag) / env.mass;
  state.tas = Math.max(0, state.tas + ax * dt);
  state.ias = state.tas;

  // Heading from banked turn
  const turnRate = (Math.tan(bankRad) * state.tas / 100) * env.bankTurnGain;
  state.heading = (state.heading + turnRate * dt) % 360;
  if (state.heading < 0) state.heading += 360;
}

/* --- Rendering --- */
function draw() {
  const w = canvas.width, h = canvas.height;
  ctx.clearRect(0,0,w,h);

  // Background: sky & ground gradient
  ctx.save();
  // Rotate around center by -roll
  const rollRad = state.roll * Math.PI/180;
  const pitchPxPerDeg = 3.2;
  const pitchOffset = state.pitch * pitchPxPerDeg;

  ctx.translate(w/2, h/2);
  ctx.rotate(-rollRad);

  // Sky
  const skyGrad = ctx.createLinearGradient(0, -h, 0, 0);
  skyGrad.addColorStop(0, '#266fe8');
  skyGrad.addColorStop(1, '#74c7ff');
  ctx.fillStyle = skyGrad;
  ctx.fillRect(-w, -h*2 + pitchOffset, w*2, h*2);

  // Ground
  const groundGrad = ctx.createLinearGradient(0, 0, 0, h);
  groundGrad.addColorStop(0, '#4aa25a');
  groundGrad.addColorStop(1, '#2d6c3a');
  ctx.fillStyle = groundGrad;
  ctx.fillRect(-w, pitchOffset, w*2, h*2);

  // Horizon line
  ctx.strokeStyle = '#ffffff';
  ctx.lineWidth = 2;
  ctx.beginPath();
  ctx.moveTo(-w, pitchOffset);
  ctx.lineTo(w, pitchOffset);
  ctx.stroke();

  // Pitch ladder
  ctx.strokeStyle = '#e6eef6';
  for (let p = -30; p <= 30; p += 5) {
    const y = pitchOffset - p*pitchPxPerDeg;
    const len = (p % 10 === 0) ? 80 : 40;
    ctx.beginPath();
    ctx.moveTo(-len, y);
    ctx.lineTo(len, y);
    ctx.stroke();
    if (p !== 0 && p % 10 === 0) {
      ctx.fillStyle = '#e6eef6';
      ctx.font = '12px system-ui';
      ctx.fillText(String(p), -len-22, y+4);
      ctx.fillText(String(p), len+8, y+4);
    }
  }

  // Runway reference (far)
  ctx.fillStyle = 'rgba(255,255,255,0.5)';
  ctx.fillRect(-24, pitchOffset + 160, 48, 6);

  ctx.restore();

  // Fixed aircraft symbol
  ctx.strokeStyle = '#00d2ff';
  ctx.lineWidth = 3;
  ctx.beginPath();
  ctx.moveTo(w/2 - 30, h/2); ctx.lineTo(w/2 + 30, h/2);
  ctx.moveTo(w/2, h/2); ctx.lineTo(w/2, h/2 + 16);
  ctx.stroke();

  // HUD tapes
  spdVal.textContent = String(Math.round(state.ias*1.943));     // m/s -> kn
  altVal.textContent = String(Math.round(state.alt*3.281));      // m -> ft
  hdgVal.textContent = String(Math.round(state.heading)).padStart(3, '0');

  // Throttle UI
  const thrHeight = Math.round(state.throttle * 100);
  thrFill.style.height = thrHeight + '%';
  thrPct.textContent = Math.round(state.throttle * 100) + '%';
}

/* --- Loop --- */
function loop() {
  step(env.dt);
  draw();
  requestAnimationFrame(loop);
}
loop();
</script>
</body>
</html>
