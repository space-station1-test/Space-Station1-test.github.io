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
    button:disabled { opacity: 0.3; cursor: not-allowed; }
    #shop { margin-top: 10px; border-top: 1px solid #4af; padding-top: 10px; }
    .section-title { font-size: 10px; color: #4af; font-weight: bold; text-transform: uppercase; margin-bottom: 5px; display: block; }
</style>
</head>
<body>

<div class="ui" id="mainUI">
    <div id="stats">
        <div id="coinsDisplay">Coins: 0</div>
        <div id="gemsDisplay">Gems: 0</div>
        <div id="highscoreDisplayUI" style="color: #4af; font-size: 11px;">Best: 0</div>
    </div>
    <button onclick="restartGame()">Restart</button>
    
    <div id="weaponShop">
        <span class="section-title">Våpen</span>
        <button id="pistolBtn" onclick="selectWeapon('pistol')">Pistol</button>
        <button id="upgPistol" onclick="upgradeWeapon('pistol')">Oppgrader Pistol</button>
        <button id="smgBtn" onclick="buyWeapon('smg', 1000)">Kjøp SMG (1000c)</button>
        <button id="shotgunBtn" onclick="buyWeapon('shotgun', 750)">Kjøp Shotgun (750c)</button>
        <button id="rebirthBtn" onclick="rebirth()" style="display:none; background: gold; color: black;">REBIRTH (500c)</button>
    </div>

    <div id="shop">
        <span class="section-title">Boosters</span>
        <button id="armorBtn" onclick="buyBooster('armor', 50)">🛡️ Armor (50g)</button>
        <button id="dmgBtn" onclick="buyBooster('doubleDamage', 50)">🔥 2x Dmg (50g)</button>
    </div>
</div>

<canvas id="game" width="400" height="600"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

// Spill-tilstand
let player, enemies, bullets, particles, stars;
let score = 0, gameOver = false, paused = false, shootCooldown = 0;
let keys = {};
let boss = null;
let bossSpawned = false;

// Lagring og Stats
let coins = Number(localStorage.getItem("coins")) || 100;
let gems = Number(localStorage.getItem("gems")) || 10;
let highscore = Number(localStorage.getItem("highscore")) || 0;
let weaponsOwned = JSON.parse(localStorage.getItem("weaponsOwned")) || { pistol: true, smg: false, shotgun: false };
let weaponLevels = JSON.parse(localStorage.getItem("weaponLevels")) || { pistol: 0, smg: 0, shotgun: 0 };
let activeWeapon = localStorage.getItem("activeWeapon") || "pistol";
let boosters = { armor: false, doubleDamage: false };

const weaponConfigs = {
    pistol: { cooldown: [25, 18, 12], type: "single", dmg: 1 },
    smg: { cooldown: [8, 5], type: "single", dmg: 0.5 },
    shotgun: { cooldown: [45, 30], type: "triple", dmg: 1 }
};

function updateUI() {
    document.getElementById("coinsDisplay").innerText = `Coins: ${Math.floor(coins)}`;
    document.getElementById("gemsDisplay").innerText = `Gems: ${gems}`;
    document.getElementById("highscoreDisplayUI").innerText = `Best: ${Math.floor(highscore)}`;
    document.getElementById("rebirthBtn").style.display = (weaponLevels.pistol >= 2) ? "block" : "none";
    
    // Oppdater knapper
    document.getElementById("smgBtn").style.display = weaponsOwned.smg ? "none" : "block";
    document.getElementById("shotgunBtn").style.display = weaponsOwned.shotgun ? "none" : "block";
    document.getElementById("armorBtn").style.borderColor = boosters.armor ? "#0f0" : "#4af";
    document.getElementById("dmgBtn").style.borderColor = boosters.doubleDamage ? "#0f0" : "#4af";
}

function buyWeapon(type, cost) {
    if (coins >= cost) { coins -= cost; weaponsOwned[type] = true; activeWeapon = type; saveProgress(); updateUI(); }
}

function upgradeWeapon(type) {
    let cost = (weaponLevels[type] + 1) * 300;
    if (weaponsOwned[type] && weaponLevels[type] < 2 && coins >= cost) {
        coins -= cost; weaponLevels[type]++; saveProgress(); updateUI();
    }
}

function selectWeapon(type) { if (weaponsOwned[type]) { activeWeapon = type; updateUI(); } }

function buyBooster(type, cost) {
    if (gems >= cost && !boosters[type]) { gems -= cost; boosters[type] = true; updateUI(); }
}

function rebirth() {
    if (coins >= 500) {
        coins = 100; gems += 30;
        weaponLevels = { pistol: 0, smg: 0, shotgun: 0 };
        weaponsOwned = { pistol: true, smg: false, shotgun: false };
        boosters = { armor: false, doubleDamage: false };
        activeWeapon = "pistol";
        saveProgress(); restartGame();
    }
}

function saveProgress() {
    localStorage.setItem("coins", coins); localStorage.setItem("gems", gems);
    localStorage.setItem("highscore", highscore); localStorage.setItem("activeWeapon", activeWeapon);
    localStorage.setItem("weaponsOwned", JSON.stringify(weaponsOwned));
    localStorage.setItem("weaponLevels", JSON.stringify(weaponLevels));
}

function init() {
    player = { x: 180, y: 540, w: 35, h: 35, speed: 6, alive: true, armorUsed: false };
    enemies = []; bullets = []; particles = []; boss = null; bossSpawned = false;
    stars = Array.from({length:40}, () => ({x: Math.random()*400, y: Math.random()*600, s: 1+Math.random()*2}));
    score = 0; gameOver = false; updateUI();
}

function spawnEnemy() {
    if (gameOver || boss) return;
    enemies.push({ x: Math.random()*370, y: -30, w: 30, h: 30, speed: 3 + (score/10000), hp: 1 });
}

function spawnBoss() {
    boss = { x: 100, y: -100, targetY: 60, w: 200, h: 80, hp: 200, maxHp: 200, dir: 1, speed: 2 };
    bossSpawned = true;
}

function fire() {
    const config = weaponConfigs[activeWeapon];
    const dmg = config.dmg * (boosters.doubleDamage ? 2 : 1);
    if (config.type === "triple") {
        bullets.push({x: player.x+15, y: player.y, vx: -2, vy: -10, dmg: dmg});
        bullets.push({x: player.x+15, y: player.y, vx: 0, vy: -11, dmg: dmg});
        bullets.push({x: player.x+15, y: player.y, vx: 2, vy: -10, dmg: dmg});
    } else {
        bullets.push({x: player.x+15, y: player.y, vx: 0, vy: -12, dmg: dmg});
    }
    shootCooldown = config.cooldown[weaponLevels[activeWeapon]] || 20;
}

function update() {
    if (gameOver || paused) return;

    // Boss sjekk
    if (score >= 75000 && !bossSpawned) spawnBoss();

    // Skatting
    if (shootCooldown > 0) shootCooldown--;
    if (player.alive && shootCooldown <= 0) fire();

    // Bevegelse
    if (keys['a'] || keys['arrowleft']) player.x -= player.speed;
    if (keys['d'] || keys['arrowright']) player.x += player.speed;
    player.x = Math.max(0, Math.min(400 - player.w, player.x));

    // Boss logikk
    if (boss) {
        if (boss.y < boss.targetY) boss.y += 1;
        boss.x += boss.speed * boss.dir;
        if (boss.x <= 0 || boss.x + boss.w >= 400) boss.dir *= -1;
    }

    // Fiender og kollisjon
    enemies.forEach((e, i) => {
        e.y += e.speed;
        if (player.alive && player.x < e.x + e.w && player.x + player.w > e.x && player.y < e.y + e.h && player.y + player.h > e.y) {
            if (boosters.armor && !player.armorUsed) { player.armorUsed = true; enemies.splice(i, 1); }
            else { 
                player.alive = false; 
                boosters.armor = false; boosters.doubleDamage = false; // Fjern boosters
                setTimeout(() => { gameOver = true; if(score > highscore) highscore = Math.floor(score); saveProgress(); }, 1000);
            }
        }
        if (e.y > 600) enemies.splice(i, 1);
    });

    // Kuler
    bullets.forEach((b, bi) => {
        b.x += b.vx; b.y += b.vy;
        if (b.y < -20) bullets.splice(bi, 1);

        // Treff boss
        if (boss && b.x > boss.x && b.x < boss.x + boss.w && b.y > boss.y && b.y < boss.y + boss.h) {
            boss.hp -= b.dmg;
            bullets.splice(bi, 1);
            if (boss.hp <= 0) { coins += 5000; gems += 50; boss = null; score += 10000; updateUI(); }
        }

        // Treff fiende
        enemies.forEach((e, ei) => {
            if (b.x > e.x && b.x < e.x + e.w && b.y > e.y && b.y < e.y + e.h) {
                enemies.splice(ei, 1); bullets.splice(bi, 1);
                coins += 10; score += 100; updateUI();
            }
        });
    });

    stars.forEach(s => { s.y += s.s; if(s.y > 600) s.y = 0; });
    score += 0.5;
}

function draw() {
    ctx.clearRect(0,0,400,600);
    ctx.fillStyle = "white"; stars.forEach(s => ctx.fillRect(s.x, s.y, 2, 2));
    
    if (player.alive) {
        ctx.fillStyle = (boosters.armor && !player.armorUsed) ? "#4af" : "#0f0";
        ctx.fillRect(player.x, player.y, player.w, player.h);
    }

    ctx.fillStyle = "yellow"; bullets.forEach(b => ctx.fillRect(b.x, b.y, 5, 10));
    ctx.fillStyle = "#f44"; enemies.forEach(e => ctx.fillRect(e.x, e.y, e.w, e.h));

    if (boss) {
        ctx.fillStyle = "#f0f"; ctx.fillRect(boss.x, boss.y, boss.w, boss.h);
        ctx.fillStyle = "red"; ctx.fillRect(100, 20, 200, 10);
        ctx.fillStyle = "lime"; ctx.fillRect(100, 20, 200 * (boss.hp/boss.maxHp), 10);
    }

    ctx.fillStyle = "white"; ctx.font = "16px Arial";
    ctx.fillText(`Score: ${Math.floor(score)}`, 10, 25);
    if (gameOver) { ctx.fillStyle = "red"; ctx.font = "30px Arial"; ctx.fillText("GAME OVER", 110, 300); }
}

// Kontroller
window.addEventListener("keydown", e => keys[e.key.toLowerCase()] = true);
window.addEventListener("keyup", e => keys[e.key.toLowerCase()] = false);
canvas.addEventListener("mousemove", e => {
    const rect = canvas.getBoundingClientRect();
    player.x = (e.clientX - rect.left) * (400 / rect.width) - 17;
});

function restartGame() { init(); }
init();
setInterval(spawnEnemy, 600);
function loop() { update(); draw(); requestAnimationFrame(loop); }
requestAnimationFrame(loop);
</script>
</body>
</html>
