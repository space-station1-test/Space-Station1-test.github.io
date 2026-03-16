<html lang="no">
<head>
<meta charset="UTF-8">
<title>Space Station - Destruction Edition</title>
<style>
    body { margin:0; background:black; color:white; display:flex; justify-content:center; align-items:center; height:100vh; font-family:Arial; overflow:hidden; touch-action: none; }
    canvas { background:#05080f; border:2px solid #4af; max-width: 100vw; max-height: 100vh; cursor: crosshair; }
    
    /* Høyre meny (Spillkontroll) */
    .ui { position:absolute; top:10px; right:10px; display:flex; flex-direction:column; gap:5px; z-index:10; width: 195px; background: rgba(0,0,0,0.85); padding: 10px; border-radius: 8px; border: 1px solid #4af; }
    /* Venstre meny (Skins) */
    .ui-left { position:absolute; top:10px; left:10px; display:flex; flex-direction:column; gap:5px; z-index:10; width: 160px; background: rgba(0,0,0,0.85); padding: 10px; border-radius: 8px; border: 1px solid #4af; }
    
    button { padding:8px; font-size:11px; cursor:pointer; background: #222; color: white; border: 1px solid #4af; border-radius: 4px; width: 100%; margin-bottom: 2px; }
    button:active { background: #4af; }
    button.active-wpn { background: #004400; border-color: #0f0; color: #0f0; }
    
    .section-title { font-size: 10px; color: #4af; font-weight: bold; text-transform: uppercase; margin-bottom: 5px; display: block; }
    .hidden { display: none !important; }
</style>
</head>
<body>

<div class="ui-left" id="skinUI">
    <span class="section-title">Skins</span>
    <button id="creeperBtn" onclick="endreSkin('creeper')">Buy Creeper (5000🟡)</button>
    <button id="blockBtn" onclick="endreSkin('block')">Buy Block (2000🟡)</button>
    <button onclick="endreSkin('default')">Standard 🚀</button>
</div>

<div class="ui" id="mainUI">
    <button id="toggleUIBtn" onclick="toggleUI()">Hide UI</button>
    <div id="uiContent">
        <div id="stats">
            <div id="coinsDisplay">Coins: 0</div>
            <div id="gemsDisplay">Gems: 0</div>
            <div id="highscoreDisplayUI">Highscore: 0</div>
        </div>
        <button id="pauseBtn" onclick="togglePause()">Pause</button>
        <button onclick="restartGame()">Restart</button>
    </div>
</div>

<canvas id="game" width="400" height="600"></canvas>

<script>
// Variabler
let currentSkin = localStorage.getItem("currentSkin") || "default";
let creeperOwned = JSON.parse(localStorage.getItem("creeperOwned")) || false;
let blockOwned = JSON.parse(localStorage.getItem("blockOwned")) || false;
let coins = Number(localStorage.getItem("coins")) || 100;
let player = { x: 180, y: 540, width: 35, height: 35, alive: true };
let lastTime = 0;

function endreSkin(valg) {
    if (valg === 'creeper' && (creeperOwned || coins >= 5000)) {
        if (!creeperOwned) { coins -= 5000; creeperOwned = true; localStorage.setItem("creeperOwned", true); }
        currentSkin = 'creeper';
    } else if (valg === 'block' && (blockOwned || coins >= 2000)) {
        if (!blockOwned) { coins -= 2000; blockOwned = true; localStorage.setItem("blockOwned", true); }
        currentSkin = 'block';
    } else {
        currentSkin = 'default';
    }
    localStorage.setItem("currentSkin", currentSkin);
    updateUI();
}

function updateUI() {
    document.getElementById("coinsDisplay").innerText = `Coins: ${Math.floor(coins)}`;
    // Legg til oppdatering av knapper her...
}

function draw() {
    ctx.clearRect(0, 0, 400, 600);
    if (player.alive) {
        if (currentSkin === "creeper") {
            ctx.fillStyle = "#03AC13"; ctx.fillRect(player.x, player.y, player.width, player.height);
            ctx.fillStyle = "black"; ctx.fillRect(player.x+6, player.y+8, 8, 8); ctx.fillRect(player.x+21, player.y+8, 8, 8);
        } else if (currentSkin === "block") {
            ctx.fillStyle = "#FF5733"; 
            ctx.fillRect(player.x, player.y, player.width, player.height); // Kvadrat-skin
        } else {
            ctx.fillStyle = '#0f0'; ctx.fillRect(player.x, player.y, player.width, player.height);
        }
    }
}

function loop(timestamp) {
    let deltaTime = timestamp - lastTime;
    lastTime = timestamp;
    draw();
    requestAnimationFrame(loop);
}

requestAnimationFrame(loop);
</script>
</body>
</html>
