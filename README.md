<html lang="no">
<head>
<meta charset="UTF-8">
<title>Space Station - Boss Edition</title>
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
    #stats { font-size: 13px; margin-bottom: 5px; line-height: 1.4; }
    .reset-btn { border-color: #f44; color: #f44; margin-top: 10px; font-size: 9px; }
    .hidden { display: none !important; }
</style>
</head>
<body>

<div class="ui" id="mainUI">
    <button id="toggleUIBtn" onclick="toggleUI()">Skjul UI</button>
    <div id="uiContent">
        <div id="stats">
            <div id="coinsDisplay">Coins: 0</div>
            <div id="gemsDisplay">Gems: 0</div>
            <div id="highscoreDisplayUI" style="color: #4af; font-size: 11px;">Best: 0</div>
        </div>
        <button onclick="togglePause()">Pause</button>
        <button onclick="restartGame()">Restart</button>
        
        <div id="weaponShop">
            <span class="section-title">Våpen (1-4)</span>
            <button id="pistolSelect" onclick="selectWeapon('pistol')">Pistol</button>
            <button id="unlockBtn" onclick="buyWeapon('pistol', 0)">Lås opp (1k Score)</button>
            <button id="upgradePistolBtn" onclick="upgradeWeapon('pistol')">Upg Pistol</button>
            
            <button id="smgSelect" onclick="selectWeapon('smg')">SMG</button>
            <button id="buySMGBtn" onclick="buyWeapon('smg', 1000)">SMG (1000c)</button>
            
            <button id="shotgunSelect" onclick="selectWeapon('shotgun')">Shotgun</button>
            <button id="buyShotgunBtn" onclick="buyWeapon('shotgun', 750)">Shotgun (750c)</button>

            <button id="rebirthBtn" onclick="rebirth()" style="display:none; background: gold !important; color: black; font-weight: bold;">REBIRTH (500c)</button>
        </div>

        <div id="shop">
            <span class="section-title">Boosters</span>
            <button id="armorBtn" onclick="buyBooster('armor', 50)">🛡️ Armor (50g)</button>
            <button id="doubleDamageBtn" onclick="buyBooster('doubleDamage', 50)">🔥 2x Dmg (50g)</button>
        </div>
        <button class="reset-btn" onclick="resetGameData()">RESET ALL</button>
    </div>
</div>

<canvas id="game" width="400" height="600"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
const BASE_SPEED = 1.3;

let player, enemies, bullets, stars, particles, floatingTexts;
let score = 0, gameOver = false, paused = false, shootCooldown = 0;
let keys = {};
let uiVisible = true;

// Boss variabler
let boss = null;
let bossSpawned = false;

let coins = Number(localStorage.getItem("coins")) || 100;
let gems = Number(localStorage.getItem("gems")) || 10;
let highscore = Number(localStorage.getItem("highscore")) || 0;
let activeWeapon = localStorage.getItem("activeWeapon") || "none";
let weaponsOwned = JSON.parse(localStorage.getItem("weaponsOwned")) || { pistol: false, smg: false, shotgun: false, ar: false };
let weaponLevels = JSON.parse(localStorage.getItem("weaponLevels")) || { pistol: 0, smg: 0, shotgun: 0, ar: 0 };
let boosters = { armor: false, doubleDamage: false };

const weaponConfigs = {
    pistol: { cooldown: [25, 18, 12], maxLvl: 2, type: "single", dmg: 1 },
    smg: { cooldown: [8, 5], maxLvl: 1, type: "single", dmg: 0.5 },
    shotgun: { cooldown: [45, 30], maxLvl: 1, type: "triple", dmg: 1 }
};

function toggleUI() {
    uiVisible = !uiVisible;
    document.getElementById("uiContent").classList.toggle("hidden", !uiVisible);
}

function selectWeapon(type) {
    if (weaponsOwned[type]) { activeWeapon = type; saveProgress(); updateUI(); }
}

function updateUI() {
    document.getElementById("coinsDisplay").innerText = `Coins: ${Math.floor(coins)}`;
    document.getElementById("gemsDisplay").innerText = `Gems: ${gems}`;
    document.getElementById("highscoreDisplayUI").innerText = `Best: ${Math.floor(highscore)}`;
    document.getElementById("armorBtn").style.borderColor = boosters.armor ? "#0f0" : "#4af";
    document.getElementById("doubleDamageBtn").style.borderColor = boosters.doubleDamage ? "#0f0" : "#4af";
    document.getElementById("rebirthBtn").style.display = (weaponLevels.pistol >= 2) ? "block" : "none";
}

function buyWeapon(type, cost) {
    if (coins >= cost) { coins -= cost; weaponsOwned[type] = true; activeWeapon = type; saveProgress(); updateUI(); }
}

function upgradeWeapon(type) {
    let cost = (weaponLevels[type] + 1) * 300;
    if (weaponsOwned[type] && weaponLevels[type] < weaponConfigs[type].maxLvl && coins >= cost) {
        coins -= cost; weaponLevels[type]++; saveProgress(); updateUI();
    }
}

function rebirth() {
    if (coins >= 500) {
        coins = 100; gems += 30;
        weaponLevels = { pistol: 0, smg: 0, shotgun: 0, ar: 0 };
        weaponsOwned = { pistol: false, smg: false, shotgun: false, ar: false };
        boosters = { armor: false, doubleDamage: false };
        activeWeapon = "none";
        bossSpawned = false; boss = null;
        saveProgress(); init();
    }
}

function buyBooster(type, cost) {
    if (gems >= cost && !boosters[type]) { gems -= cost; boosters[type] = true; updateUI(); saveProgress(); }
}

function saveProgress() {
    localStorage.setItem("coins", coins); localStorage.setItem("gems", gems);
    localStorage.setItem("highscore", highscore); localStorage.setItem("activeWeapon", activeWeapon);
    localStorage.setItem("weaponsOwned", JSON.stringify(weaponsOwned));
    localStorage.setItem("weaponLevels", JSON.stringify(weaponLevels));
}

function init() {
    player = { x: 180, y: 540, width: 35, height: 35, speed: 7 * BASE_SPEED, armorUsed: false, alive: true };
    enemies = []; bullets = []; particles = []; floatingTexts = [];
    stars = Array.from({length:50}, () => ({x: Math.random()*400, y: Math.random()*600, s: (1+Math.random()*2) * BASE_SPEED}));
    score = 0; bossSpawned = false; boss = null;
    gameOver = false; paused = false; updateUI();
}

function createExplosion(x, y, color, count = 20) {
    for (let i = 0; i < count; i++) {
        particles.push({x, y, vx: (Math.random()-0.5)*12, vy: (Math.random()-0.5)*12, life: 1, color});
    }
}

function spawnEnemy() {
    if (paused || gameOver || boss) return;
    enemies.push({x: Math.random()*370, y: -40, w: 30, h: 30, speedY: (2.5 + score/5000) * BASE_SPEED, color: '#f44', coins: 10, hp: 1});
}

function spawnBoss() {
    boss = {
        x: 100, y: -100, targetY: 50, w: 200, h: 80, 
        hp: 200, maxHp: 200, speedX: 2, dir: 1, 
        color: '#ff00ff'
    };
    bossSpawned = true;
}

function fire() {
    if (!player.alive || activeWeapon === "none") return;
    const config = weaponConfigs[activeWeapon];
    const damage = config.dmg * (boosters.doubleDamage ? 2 : 1);
    if (config.type === "triple") {
        bullets.push({x: player.x + 15, y: player.y, vx: -2.5, vy: -11, dmg: damage});
        bullets.push({x: player.x + 15, y: player.y, vx: 0, vy: -12, dmg: damage});
        bullets.push({x: player.x + 15, y: player.y, vx: 2.5, vy: -11, dmg: damage});
    } else {
        bullets.push({x: player.x + 15, y: player.y, vx: 0, vy: -12, dmg: damage});
    }
    shootCooldown = config.cooldown[weaponLevels[activeWeapon]];
}

function update() {
    if (gameOver || paused) return;

    if (score >= 75000 && !bossSpawned) spawnBoss();

    if (player.alive && activeWeapon !== "none" && shootCooldown <= 0) fire();
    if (shootCooldown > 0) shootCooldown--;

    if (player.alive) {
        if ((keys['a'] || keys['arrowleft']) && player.x > 0) player.x -= player.speed;
        if ((keys['d'] || keys['arrowright']) && player.x < 400 - player.width) player.x += player.speed;
    }

    if (boss) {
        if (boss.y < boss.targetY) boss.y += 1;
        boss.x += boss.speedX * boss.dir;
        if (boss.x <= 0 || boss.x + boss.w >= 400) boss.dir *= -1;

        bullets.forEach((b, bi) => {
            if (b.x > boss.x && b.x < boss.x + boss.w && b.y > boss.y && b.y < boss.y + boss.h) {
                boss.hp -= b.dmg;
                bullets.splice(bi, 1);
                createExplosion(b.x, b.y, "white", 5);
                if (boss.hp <= 0) {
                    createExplosion(boss.x + boss.w/2, boss.y + boss.h/2, "magenta", 100);
                    coins += 5000; gems += 50;
                    boss = null; score += 10000; updateUI();
                }
            }
        });
    }

    enemies.forEach((e, ei) => {
        e.y += e.speedY;
        if (player.alive && player.x < e.x + e.w && player.x + player.width > e.x && player.y < e.y + e.h && player.y + player.height > e.y) {
            if (boosters.armor && !player.armorUsed) { player.armorUsed = true; enemies.splice(ei, 1); }
            else { 
                player.alive = false; 
                boosters.armor = false; boosters.doubleDamage = false;
                setTimeout(() => { gameOver = true; if(score > highscore) highscore = Math.floor(score); saveProgress(); updateUI(); }, 1000);
            }
        }
        bullets.forEach((b, bi) => {
            if (b.x < e.x + e.w && b.x + 6 > e.x && b.y < e.y + e.h && b.y + 12 > e.y) {
                enemies.splice(ei, 1); bullets.splice(bi, 1);
                coins += 10; score += 100; updateUI();
            }
        });
        if (e.y > 600) enemies.splice(ei, 1);
    });

    bullets.forEach((b, i) => { b.y += b.vy; b.x += b.vx; if(b.y < -20) bullets.splice(i,1); });
    particles.forEach((p, i) => { p.x += p.vx; p.y += p.vy; p.life -= 0.02; if(p.life <= 0) particles.splice(i,1); });
    score += 0.3;
}

function draw() {
    ctx.clearRect(0,0,400,600);
    stars.forEach(s => { ctx.fillStyle='white'; ctx.fillRect(s.x,s.y,2,2); s.y += s.s; if(s.y > 600) s.y = 0; });
    if (player.alive) {
        ctx.fillStyle = (boosters.armor && !player.armorUsed) ? '#4af' : '#0f0';
        ctx.fillRect(player.x, player.y, player.width, player.height);
    }
    bullets.forEach(b => { ctx.fillStyle = 'yellow'; ctx.fillRect(b.x, b.y, 6, 12); });
    enemies.forEach(e => { ctx.fillStyle = e.color; ctx.fillRect(e.x, e.y, e.w, e.h); });
    particles.forEach(p => { ctx.globalAlpha = p.life; ctx.fillStyle = p.color; ctx.fillRect(p.x, p.y, 4, 4); });
    ctx.globalAlpha = 1;

    if (boss) {
        ctx.fillStyle = boss.color;
        ctx.fillRect(boss.x, boss.y, boss.w, boss.h);
        ctx.fillStyle = "red"; ctx.fillRect(100, 20, 200, 10);
        ctx.fillStyle = "lime"; ctx.fillRect(100, 20, 200 * (boss.hp / boss.maxHp), 10);
    }

    ctx.fillStyle = 'white'; ctx.font = 'bold 16px Arial';
    ctx.fillText(`Score: ${Math.floor(score)}`, 10, 25);
    if(gameOver) { ctx.fillStyle="red"; ctx.font="30px Arial"; ctx.fillText("GAME OVER", 110, 300); }
}

window.addEventListener("keydown", e => { keys[e.key.toLowerCase()] = true; });
window.addEventListener("keyup", e => { keys[e.key.toLowerCase()] = false; });

const handleMove = (e) => {
    if(paused || gameOver || !player.alive) return;
    const rect = canvas.getBoundingClientRect();
    const clientX = e.touches ? e.touches[0].clientX : e.clientX;
    player.x = (clientX - rect.left) * (400 / rect.width) - 17;
};
canvas.addEventListener("mousemove", handleMove);
canvas.addEventListener("touchmove", (e) => { e.preventDefault(); handleMove(e); }, { passive: false });

function togglePause() { paused = !paused; }
function restartGame() { init(); }
function resetGameData() { if(confirm("Slette alt?")) { localStorage.clear(); location.reload(); } }

init();
setInterval(spawnEnemy, 500); 

function loop() {
    update();
    draw();
    requestAnimationFrame(loop);
}
requestAnimationFrame(loop);
</script>
</body>
</html>
