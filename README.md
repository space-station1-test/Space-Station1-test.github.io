<html lang="no">
<head>
<meta charset="UTF-8">
<title>Space Station - Fixed Edition</title>
<style>
    body { margin:0; background:black; color:white; display:flex; justify-content:center; align-items:center; height:100vh; font-family:Arial; overflow:hidden; touch-action: none; }
    canvas { background:#05080f; border:2px solid #4af; max-width: 100vw; max-height: 100vh; cursor: crosshair; }
    .ui { position:absolute; top:10px; right:10px; display:flex; flex-direction:column; gap:5px; z-index:10; width: 195px; background: rgba(0,0,0,0.85); padding: 10px; border-radius: 8px; border: 1px solid #4af; max-height: 90vh; overflow-y: auto; }
    button { padding:8px; font-size:11px; cursor:pointer; background: #222; color: white; border: 1px solid #4af; border-radius: 4px; width: 100%; margin-bottom: 2px; }
    button:active { background: #4af; }
    button.active-wpn { background: #004400; border-color: #0f0; color: #0f0; }
    #shop { margin-top: 10px; border-top: 1px solid #4af; padding-top: 10px; }
    .section-title { font-size: 10px; color: #4af; font-weight: bold; text-transform: uppercase; margin-bottom: 5px; display: block; }
    .hidden { display: none !important; }
    .wpn-group { margin-bottom: 10px; padding: 5px; border-radius: 4px; background: rgba(255,255,255,0.05); }
</style>
</head>
<body>

<div class="ui" id="mainUI">
    <button id="toggleUIBtn" onclick="toggleUI()">Skjul UI</button>
    <div id="uiContent">
        <div id="stats">
            <div id="coinsDisplay">Coins: 0</div>
            <div id="gemsDisplay">Gems: 0</div>
        </div>
        <button onclick="togglePause()">Pause</button>
        <button onclick="restartGame()">Restart</button>
        
        <div id="skinShop">
            <span class="section-title">Skins</span>
            <button onclick="changeSkin('creeper')">Creeper 🟩</button>
            <button onclick="changeSkin('default')">Standard 🚀</button>
        </div>

        <div id="weaponShop">
            <span class="section-title">Våpen</span>
            <button id="pistolSelect" onclick="selectWeapon('pistol')">Pistol</button>
            <button id="smgSelect" onclick="selectWeapon('smg')">SMG</button>
            <button id="buySMGBtn" onclick="buyWeapon('smg', 1000)">Kjøp SMG (1000🟡)</button>
        </div>
        <button style="color:red; font-size:9px;" onclick="resetGameData()">Slett alt</button>
    </div>
</div>

<canvas id="game" width="400" height="600"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

// SPILLVARIABLER
let player, enemies, bullets, stars, particles, floatingTexts;
let score = 0, highscore = 0, coins = 100, gems = 10;
let gameOver = false, paused = false, shootCooldown = 0;
let keys = {};
let currentSkin = "default";
let uiVisible = true;

// LAST BILDER
const creeperImg = new Image();
creeperImg.src = "https://minecraftfaces.com/wp-content/themes/minecraftfaces/faces/creeper-face.png";

// WEAPON CONFIG
const weaponConfigs = {
    pistol: { cooldown: 20, dmg: 1, type: "single" },
    smg: { cooldown: 7, dmg: 0.5, type: "single" }
};
let activeWeapon = "pistol";
let weaponsOwned = { pistol: true, smg: false };

function changeSkin(skin) {
    currentSkin = skin;
    console.log("Byttet skin til:", skin);
}

function toggleUI() {
    uiVisible = !uiVisible;
    document.getElementById("uiContent").classList.toggle("hidden", !uiVisible);
}

function init() {
    player = { x: 180, y: 540, width: 35, height: 35, speed: 5, alive: true };
    enemies = []; bullets = []; particles = []; floatingTexts = [];
    stars = Array.from({length:40}, () => ({x: Math.random()*400, y: Math.random()*600, s: 1+Math.random()*2}));
    score = 0;
    gameOver = false;
}

function spawnEnemy() {
    if (paused || gameOver) return;
    enemies.push({
        x: Math.random()*370, y: -40, w: 30, h: 30, 
        speedY: 2 + (score/5000), color: '#f44', hp: 1
    });
}

function update() {
    if (gameOver || paused) return;

    // Bevegelse
    if (keys['a'] && player.x > 0) player.x -= player.speed;
    if (keys['d'] && player.x < 400 - player.width) player.x += player.speed;

    // Skyt
    if (shootCooldown > 0) shootCooldown--;
    if (shootCooldown <= 0 && player.alive) {
        bullets.push({x: player.x + 15, y: player.y, vy: -10});
        shootCooldown = weaponConfigs[activeWeapon].cooldown;
    }

    // Oppdater stjerner
    stars.forEach(s => { s.y += s.s; if(s.y > 600) s.y = 0; });

    // Oppdater skudd
    bullets.forEach((b, i) => {
        b.y += b.vy;
        if (b.y < -20) bullets.splice(i, 1);
    });

    // Oppdater fiender
    enemies.forEach((e, ei) => {
        e.y += e.speedY;
        
        // Sjekk kollisjon med spiller
        if (player.alive && player.x < e.x + e.w && player.x + player.width > e.x && player.y < e.y + e.h) {
            gameOver = true;
        }

        // Sjekk treff av skudd
        bullets.forEach((b, bi) => {
            if (b.x > e.x && b.x < e.x + e.w && b.y < e.y + e.h) {
                enemies.splice(ei, 1);
                bullets.splice(bi, 1);
                score += 100;
                coins += 1;
                updateUI();
            }
        });

        if (e.y > 600) enemies.splice(ei, 1);
    });
    score += 1;
}

function draw() {
    ctx.clearRect(0,0,400,600);
    
    // Tegn bakgrunn
    ctx.fillStyle = 'white';
    stars.forEach(s => ctx.fillRect(s.x, s.y, 2, 2));

    // TEGN SPILLER (Her skjedde feilen før)
    if (player.alive) {
        if (currentSkin === "creeper" && creeperImg.complete) {
            ctx.drawImage(creeperImg, player.x, player.y, player.width, player.height);
        } else {
            ctx.fillStyle = "#0f0";
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }
    }

    // Tegn skudd
    ctx.fillStyle = "yellow";
    bullets.forEach(b => ctx.fillRect(b.x, b.y, 5, 10));

    // Tegn fiender
    enemies.forEach(e => {
        ctx.fillStyle = e.color;
        ctx.fillRect(e.x, e.y, e.w, e.h);
    });

    // UI på canvas
    ctx.fillStyle = "white";
    ctx.font = "16px Arial";
    ctx.fillText("Score: " + Math.floor(score), 10, 25);

    if (gameOver) {
        ctx.fillStyle = "red";
        ctx.font = "40px Arial";
        ctx.fillText("GAME OVER", 80, 300);
    }
}

function updateUI() {
    document.getElementById("coinsDisplay").innerText = "Coins: " + coins;
}

// Kontroller for iPad/Mobil
canvas.addEventListener("touchmove", (e) => {
    e.preventDefault();
    let rect = canvas.getBoundingClientRect();
    let touchX = e.touches[0].clientX - rect.left;
    player.x = (touchX * (400 / rect.width)) - player.width / 2;
}, { passive: false });

window.addEventListener("keydown", e => keys[e.key.toLowerCase()] = true);
window.addEventListener("keyup", e => keys[e.key.toLowerCase()] = false);

function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

function buyWeapon(w, cost) {
    if (coins >= cost) { coins -= cost; weaponsOwned[w] = true; activeWeapon = w; updateUI(); }
}

function selectWeapon(w) { if(weaponsOwned[w]) activeWeapon = w; }
function restartGame() { init(); }
function togglePause() { paused = !paused; }
function resetGameData() { localStorage.clear(); location.reload(); }

init();
setInterval(spawnEnemy, 600);
gameLoop();
</script>
</body>
</html>
