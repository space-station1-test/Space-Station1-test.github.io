<html lang="no">
<head>
    <meta charset="UTF-8">
    <title>Space Station - The Void Awakening</title>
    
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="mobile-web-app-capable" content="yes">
    
    <link rel="apple-touch-icon" href="icon.png">
    
    <link rel="icon" type="image/png" href="icon.png">
    <link rel="manifest" href="manifest.json">

    <style>
        body { margin:0; background:black; color:white; display:flex; justify-content:center; align-items:center; height:100vh; font-family:Arial; overflow:hidden; touch-action: none; }
        canvas { background:#05080f; border:2px solid #4af; max-width: 100vw; max-height: 100vh; cursor: crosshair; }
        .ui { position:absolute; top:10px; right:10px; display:flex; flex-direction:column; gap:5px; z-index:10; width: 195px; background: rgba(0,0,0,0.85); padding: 10px; border-radius: 8px; border: 1px solid #4af; max-height: 90vh; overflow-y: auto; }
        .ui-left { position: absolute; top: 10px; left: 10px; display: flex; flex-direction: column; gap: 5px; z-index: 10; width: 160px; background: rgba(0,0,0,0.85); padding: 10px; border-radius: 8px; border: 1px solid #4af; }
        button { padding:8px; font-size:11px; cursor:pointer; background: #222; color: white; border: 1px solid #4af; border-radius: 4px; width: 100%; margin-bottom: 2px; }
        button:active { background: #4af; }
        button.active-wpn { background: #004400; border-color: #0f0; color: #0f0; }
        button:disabled { opacity: 0.3; cursor: not-allowed; }
        #shop { margin-top: 10px; border-top: 1px solid #4af; padding-top: 10px; }
        .section-title { font-size: 10px; color: #4af; font-weight: bold; text-transform: uppercase; margin-bottom: 5px; display: block; }
        #stats { font-size: 13px; margin-bottom: 5px; line-height: 1.4; }
        .reset-btn { border-color: #f44; color: #f44; margin-top: 10px; font-size: 9px; }
        .hidden { display: none !important; }
        .wpn-group { margin-bottom: 10px; padding: 5px; border-radius: 4px; background: rgba(255,255,255,0.05); }
    </style>
</head>
<body>

<div class="ui-left" id="skinShop">
    <div id="shop">
        <span class="section-title">Skins</span>
        <button id="voidBtn" onclick="endreSkin('Void Overlord')" style="display:none; background: #1a0033 !important; border-color: #a0f !important; color: #d8f !important; font-weight: bold;">Void Overlord 🔮</button>
        <button id="coreBtn" onclick="endreSkin('The Core')">Buy The Core (7000🟡)</button>
        <button id="pigBtn" onclick="endreSkin('pig')">Buy Pig 🐷(5500🟡)</button>
        <button id="StrikerBtn" onclick="endreSkin('Striker')">Buy Striker 🚀(5000🟡)</button>
        <button onclick="endreSkin('default')">Standard 🟩</button>
        
        <span class="section-title">Boosters (Gems)</span>
        <button id="armorBtn" onclick="buyBooster('armor', 50)">🛡️Armor (50💎)</button>
        <button id="doubleDamageBtn" onclick="buyBooster('doubleDamage', 50)">🔥2x Dmg (50💎)</button>
        <button id="slowEnemiesBtn" onclick="buyBooster('slowEnemies', 50)">❄️Slow (50💎)</button>
    </div>
</div>

<div class="ui" id="mainUI">
    <button id="toggleUIBtn" onclick="toggleUI()">Hide UI</button>
    <div id="uiContent">
        <div id="stats">
            <div id="coinsDisplay">Coins: 0</div>
            <div id="gemsDisplay">Gems: 0</div>
            <div id="highscoreDisplayUI" style="color: #4af; font-size: 11px;">Highscore: 0</div>
        </div>
        <button id="pauseBtn" onclick="togglePause()">Pause</button>
        <button onclick="restartGame()">Restart</button>
        
        <div id="weaponShop">
            <span class="section-title">Weapons (1-5)</span>
            <div class="wpn-group">
                <button id="pistolSelect" onclick="selectWeapon('pistol')">Pistol</button>
                <button id="unlockBtn" onclick="buyWeapon('pistol', 0)">Unlock at (1k Score)</button>
                <button id="upgradePistolBtn" onclick="upgradeWeapon('pistol')">Upgrade</button>
            </div>
            <div class="wpn-group">
                <button id="smgSelect" onclick="selectWeapon('smg')">SMG</button>
                <button id="buySMGBtn" onclick="buyWeapon('smg', 1100)">SMG (1100🟡)</button>
                <button id="upgradeSMGBtn" onclick="upgradeWeapon('smg')">Upgrade</button>
            </div>
            <div class="wpn-group">
                <button id="shotgunSelect" onclick="selectWeapon('shotgun')">Shotgun</button>
                <button id="buyShotgunBtn" onclick="buyWeapon('shotgun', 750)">Shotgun (750🟡)</button>
                <button id="upgradeShotgunBtn" onclick="upgradeWeapon('shotgun')">Upgrade</button>
            </div>
            <div class="wpn-group">
                <button id="arSelect" onclick="selectWeapon('ar')">AR</button>
                <button id="buyARBtn" onclick="buyWeapon('ar', 1200)">AR (1200🟡)</button>
                <button id="upgradeARBtn" onclick="upgradeWeapon('ar')">Upgrade</button>
            </div>
            <div class="wpn-group" id="sniperGroup" style="display: none;">
                <button id="sniperSelect" onclick="selectWeapon('sniper')">Sniper</button>
                <button id="sniperStatus" disabled style="color: #ff0; border-color: #ff0;">LÅST OPP AV BOSS 🎯</button>
            </div>
            <button id="rebirthBtn" onclick="rebirth()" style="display:none; background: gold !important; color: black; font-weight: bold;">REBIRTH (500🟡)</button>
        </div>

        <button class="reset-btn" onclick="resetGameData()">RESET ALL DATA</button>
    </div>
</div>

<div id="loginScreen" style="position: absolute; width: 400px; height: 600px; background: rgba(5, 8, 15, 0.95); border: 2px solid #4af; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 100; border-radius: 8px; font-family: Arial, sans-serif; box-sizing: border-box; padding: 20px;">
    <h2 style="color: #4af; text-transform: uppercase; margin-bottom: 20px; font-size: 18px; letter-spacing: 2px;">Space Station Login</h2>
    
    <div style="display: flex; flex-direction: column; gap: 10px; width: 80%;">
        <input type="text" id="loginUser" placeholder="Brukernavn" style="padding: 10px; background: #111; color: white; border: 1px solid #4af; border-radius: 4px; outline: none;">
        <input type="password" id="loginCode" placeholder="Kode (PIN)" style="padding: 10px; background: #111; color: white; border: 1px solid #4af; border-radius: 4px; outline: none;">
        
        <button onclick="validerInnlogging()" style="padding: 10px; background: #004400; color: #0f0; border: 1px solid #0f0; border-radius: 4px; cursor: pointer; font-weight: bold; margin-top: 10px;">LOGG INN</button>
        <button onclick="registrerBruker()" style="padding: 10px; background: #222; color: #4af; border: 1px solid #4af; border-radius: 4px; cursor: pointer;">REGISTRER NY BRUKER</button>
        
        <p id="loginMessage" style="color: #ff4444; font-size: 12px; text-align: center; margin-top: 10px; min-height: 15px;"></p>
    </div>
</div>

<canvas id="game" width="400" height="600"></canvas>

<script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore-compat.js"></script>

<script>
const firebaseConfig = {
  apiKey: "AIzaSyC_9tm_EelwGyBMjKfjqCZXbr4_fEuycWs",
  authDomain: "space-station1-base.firebaseapp.com",
  projectId: "space-station1-base",
  storageBucket: "space-station1-base.firebasestorage.app",
  messagingSenderId: "624029879873",
  appId: "1:624029879873:web:32b5bf9453c7ec033000c0"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
const BASE_SPEED = 1.3;

let player, enemies, bullets, stars, particles, floatingTexts;
let enemyBullets = []; 
let powerups = []; 
let rainbowTimer = 0; 
let score = 0, gameOver = false, paused = false, shootCooldown = 0;
let keys = {};
let uiVisible = true;
let gemMilestone = 10000;
let lastTime = 0; 

// Boss-tilstander
let bossSpawned = false;
let activeBoss = null;

// Spill-tilstander 
let currentSkin = "default";
let StrikerOwned = false;
let TheCoreOwned = false;
let pigOwned = false;
let voidOverlordOwned = false; 
let coins = 0;
let gems = 0;
let highscore = 0;
let activeWeapon = "none";
let weaponsOwned = { pistol: false, smg: false, shotgun: false, ar: false, sniper: false };
let weaponLevels = { pistol: 0, smg: 0, shotgun: 0, ar: 0, sniper: 0 };
let boosters = { armor: false, doubleDamage: false, slowEnemies: false };
let aktivBruker = null;

const weaponConfigs = {
    pistol: { cooldown: [25, 21, 18], maxLvl: 2, type: "single", dmg: 1 },
    smg: { cooldown: [8, 5], maxLvl: 1, type: "single", dmg: 0.5 },
    shotgun: { cooldown: [45, 30], maxLvl: 1, type: "triple", dmg: 1 },
    ar: { cooldown: [14, 11], maxLvl: 1, type: "fast", dmg: 1 },
    sniper: { cooldown: [30], maxLvl: 0, type: "single", dmg: 4 } 
};

function endreSkin(valg) {
    if (valg === 'Striker') {
        if (StrikerOwned) { currentSkin = 'Striker'; }
        else if (coins >= 5000) { coins -= 5000; StrikerOwned = true; currentSkin = 'Striker'; }
    } 
    else if (valg === 'The Core') { 
        if (TheCoreOwned) { currentSkin = 'The Core'; }
        else if (coins >= 7000) { coins -= 7000; TheCoreOwned = true; currentSkin = 'The Core'; }
    }
    else if (valg === 'pig') {
        if (pigOwned) { currentSkin = 'pig'; }
        else if (coins >= 5500) { coins -= 5500; pigOwned = true; currentSkin = 'pig'; }
    } 
    else if (valg === 'Void Overlord') {
        if (voidOverlordOwned) { currentSkin = 'Void Overlord'; }
    }
    else { currentSkin = "default"; }
    saveProgress();
    updateUI();
}

function toggleUI() {
    uiVisible = !uiVisible;
    document.getElementById("uiContent").classList.toggle("hidden", !uiVisible);
    const leftUI = document.getElementById("skinShop");
    if (leftUI) leftUI.classList.toggle("hidden", !uiVisible);
    document.getElementById("toggleUIBtn").innerText = uiVisible ? "Hide UI" : "Show UI";
}

function selectWeapon(type) {
    if (weaponsOwned[type]) { activeWeapon = type; saveProgress(); updateUI(); }
}

function updateUI() {
    document.getElementById("coinsDisplay").innerText = `Coins: ${Math.floor(coins)}`;
    document.getElementById("gemsDisplay").innerText = `Gems: ${gems}`;
    document.getElementById("highscoreDisplayUI").innerText = `Best: ${Math.floor(highscore)}`;

    document.getElementById("unlockBtn").style.display = weaponsOwned.pistol ? "none" : "block";
    document.getElementById("unlockBtn").disabled = (highscore < 1000 && score < 1000);

    const upgPistol = document.getElementById("upgradePistolBtn");
    upgPistol.style.display = weaponsOwned.pistol ? "block" : "none";
    upgPistol.innerText = weaponLevels.pistol >= 2 ? "Maxed" : `Upgrade (${(weaponLevels.pistol + 1) * 300}🟡)`;
    upgPistol.disabled = weaponLevels.pistol >= 2 || coins < (weaponLevels.pistol + 1) * 300;

    document.getElementById("buySMGBtn").style.display = weaponsOwned.smg ? "none" : "block";
    const upgSMG = document.getElementById("upgradeSMGBtn");
    upgSMG.style.display = weaponsOwned.smg ? "block" : "none";
    upgSMG.innerText = weaponLevels.smg >= 1 ? "Maxed" : "Upgrade (1100🟡)";
    upgSMG.disabled = weaponLevels.smg >= 1 || coins < 1100;

    document.getElementById("buyShotgunBtn").style.display = weaponsOwned.shotgun ? "none" : "block";
    const upgShotgun = document.getElementById("upgradeShotgunBtn");
    upgShotgun.style.display = weaponsOwned.shotgun ? "block" : "none";
    upgShotgun.innerText = weaponLevels.shotgun >= 1 ? "Maxed" : "Upgrade (1000🟡)";
    upgShotgun.disabled = weaponLevels.shotgun >= 1 || coins < 1000;

    document.getElementById("buyARBtn").style.display = weaponsOwned.ar ? "none" : "block";
    const upgAR = document.getElementById("upgradeARBtn");
    upgAR.style.display = weaponsOwned.ar ? "block" : "none";
    upgAR.innerText = weaponLevels.ar >= 1 ? "Maxed" : "Upgrade (1500🟡)";
    upgAR.disabled = weaponLevels.ar >= 1 || coins < 1500;

    if (weaponsOwned.sniper) {
        document.getElementById("sniperGroup").style.display = "block";
    }

    if (voidOverlordOwned) {
        const vBtn = document.getElementById("voidBtn");
        vBtn.style.display = "block";
        vBtn.innerText = currentSkin === 'Void Overlord' ? "Void Overlord (Equipped)" : "Use Void Overlord 🔮";
    }

    ["pistol", "smg", "shotgun", "ar", "sniper"].forEach(w => {
        const btn = document.getElementById(w + "Select");
        if(btn) {
            btn.style.display = weaponsOwned[w] ? "block" : "none";
            btn.className = activeWeapon === w ? "active-wpn" : "";
            btn.innerText = activeWeapon === w ? w.toUpperCase() + " (equipped)" : "Use " + w;
        }
    });
    document.getElementById("rebirthBtn").style.display = (weaponLevels.pistol >= 2) ? "block" : "none";
    document.getElementById("armorBtn").style.borderColor = boosters.armor ? "#2f6" : "#4af";
    document.getElementById("doubleDamageBtn").style.borderColor = boosters.doubleDamage ? "#0f0" : "#4af";
    document.getElementById("slowEnemiesBtn").style.borderColor = boosters.slowEnemies ? "#0f0" : "#4af";
    
    const pBtn = document.getElementById("pigBtn");
    if (pBtn) {
        if (pigOwned) {
            pBtn.innerText = currentSkin === 'pig' ? "Pig (Equipped)" : "Use Pig";
            pBtn.style.borderColor = "#f9f";
        } else { pBtn.innerText = "Buy Pig (5500🟡)"; pBtn.disabled = coins < 5500; }
    }
    
    const tBtn = document.getElementById("coreBtn");
    if (tBtn) {
        if (TheCoreOwned) {
            tBtn.innerText = currentSkin === 'The Core' ? "The Core (Equipped)" : "Use The Core";
            tBtn.style.borderColor = "#f0f";
        } else { tBtn.innerText = "Buy The Core (7000🟡)"; tBtn.disabled = coins < 7000; }
    }

    const cBtn = document.getElementById("StrikerBtn");
    if (cBtn) {
        if (StrikerOwned) {
            cBtn.innerText = currentSkin === 'Striker' ? "Bolt (Equipped)" : "Use Bolt";
            cBtn.style.borderColor = "#0f0";
        } else { cBtn.innerText = "Buy Bolt (5000🟡)"; cBtn.disabled = coins < 5000; }
    }
}

function buyWeapon(type, cost) {
    if (coins >= cost) { coins -= cost; weaponsOwned[type] = true; activeWeapon = type; saveProgress(); updateUI(); }
}

function upgradeWeapon(type) {
    let cost = 0;
    if(type === 'pistol') cost = (weaponLevels.pistol + 1) * 300;
    else if(type === 'smg') cost = 1100;
    else if(type === 'shotgun') cost = 1000;
    else if(type === 'ar') cost = 1500;
    if (weaponsOwned[type] && weaponLevels[type] < weaponConfigs[type].maxLvl && coins >= cost) {
        coins -= cost; weaponLevels[type]++; activeWeapon = type; saveProgress(); updateUI();
    }
}

function rebirth() {
    if (coins >= 500) {
        coins = 100; gems += 30;
        weaponLevels = { pistol: 0, smg: 0, shotgun: 0, ar: 0, sniper: 0 };
        weaponsOwned = { pistol: false, smg: false, shotgun: false, ar: false, sniper: weaponsOwned.sniper }; 
        boosters = { armor: false, doubleDamage: false, slowEnemies: false };
        activeWeapon = "none";
        saveProgress(); init();
    }
}

function buyBooster(type, cost) {
    if (gems >= cost && !boosters[type]) { gems -= cost; boosters[type] = true; updateUI(); saveProgress(); }
}

function saveProgress() {
    if (!aktivBruker) return;
    db.collection("users").doc(aktivBruker).set({
        coins: coins,
        gems: gems,
        highscore: highscore,
        activeWeapon: activeWeapon,
        weaponsOwned: weaponsOwned,
        weaponLevels: weaponLevels,
        currentSkin: currentSkin,
        StrikerOwned: StrikerOwned,
        TheCoreOwned: TheCoreOwned,
        pigOwned: pigOwned,
        voidOverlordOwned: voidOverlordOwned
    }, { merge: true }).catch(err => console.error("Feil ved lagring:", err));
}

function lastInnBrukerData() {
    if (!aktivBruker) return Promise.resolve();
    return db.collection("users").doc(aktivBruker).get().then((doc) => {
        if (doc.exists) {
            const data = doc.data();
            coins = data.coins || 0;
            gems = data.gems || 0;
            highscore = data.highscore || 0;
            activeWeapon = data.activeWeapon || "none";
            weaponsOwned = data.weaponsOwned || { pistol: false, smg: false, shotgun: false, ar: false, sniper: false };
            weaponLevels = data.weaponLevels || { pistol: 0, smg: 0, shotgun: 0, ar: 0, sniper: 0 };
            currentSkin = data.currentSkin || "default";
            StrikerOwned = data.StrikerOwned || false;
            TheCoreOwned = data.TheCoreOwned || false;
            pigOwned = data.pigOwned || false;
            voidOverlordOwned = data.voidOverlordOwned || false;
        }
        updateUI();
    }).catch(err => console.error("Feil ved innlasting:", err));
}

function init() {
    player = { x: 180, y: 540, width: 35, height: 35, speed: 7 * BASE_SPEED, armorUsed: false, alive: true };
    enemies = []; bullets = []; enemyBullets = []; particles = []; floatingTexts = [];
    stars = Array.from({length:50}, () => ({x: Math.random()*400, y: Math.random()*600, s: (1+Math.random()*2) * BASE_SPEED}));
    score = 0; gemMilestone = 10000;
    gameOver = false; paused = false; shootCooldown = 0;
    bossSpawned = false; activeBoss = null;
    updateUI();
    powerups = []; rainbowTimer = 0;
}

function createExplosion(x, y, color, count = 20) {
    for (let i = 0; i < count; i++) {
        particles.push({x, y, vx: (Math.random()-0.5)*12, vy: (Math.random()-0.5)*12, life: 1, color});
    }
}

function spawnBoss() {
    bossSpawned = true;
    activeBoss = {
        x: 140, y: -100, targetY: 80, w: 120, h: 80,
        speedX: 1.5 * BASE_SPEED, centerX: 140, angle: 0,
        hp: 150, maxHp: 150, shootTimer: 0, type: 'boss'
    };
}

function spawnEnemy() {
    if (paused || gameOver || activeBoss) return; 
    let extraChance = Math.min(0.2, score / 100000);
    let r = Math.random();
    if (r < 0.15 && score > 2000) {
        let hp = 6 + Math.floor(score / 10000);
        enemies.push({x: Math.random()*350, y: -50, w: 45, h: 45, speedY: 1.2 * BASE_SPEED, color: '#800', coins: 50, hp: hp, maxHp: hp, isHeavy: true, type: 'heavy'});
    } else if (r < 0.30 + extraChance && score > 1000) {
        enemies.push({x: Math.random()*300 + 50, y: -40, w: 25, h: 25, speedY: 2 * BASE_SPEED, color: '#a0f', coins: 20, hp: 1, isHeavy: false, type: 'sinus', centerX: 0, angle: 0});
    } else {
        enemies.push({x: Math.random()*370, y: -40, w: 30, h: 30, speedY: (2.5 + score/5000) * BASE_SPEED, color: '#f44', coins: 10, hp: 1, isHeavy: false, type: 'normal'});
    }
}

function fire() {
    if (!player.alive || activeWeapon === "none") return;
    const config = weaponConfigs[activeWeapon];
    const lvl = weaponLevels[activeWeapon];
    const damage = config.dmg * (boosters.doubleDamage ? 2 : 1);
    
    if (config.type === "triple") {
        bullets.push({x: player.x + 15, y: player.y, vx: -2.5, vy: -11, dmg: damage});
        bullets.push({x: player.x + 15, y: player.y, vx: 0, vy: -12, dmg: damage});
        bullets.push({x: player.x + 15, y: player.y, vx: 2.5, vy: -11, dmg: damage});
    } else if (activeWeapon === "sniper") {
        bullets.push({x: player.x + 12, y: player.y, vx: 0, vy: -16, dmg: damage, isSniper: true});
    } else {
        bullets.push({x: player.x + 15, y: player.y, vx: 0, vy: -12, dmg: damage});
    }
    shootCooldown = rainbowTimer > 0 ? config.cooldown[lvl] / 3 : config.cooldown[lvl]; 
}

function killPlayer() {
    if (boosters.armor && !player.armorUsed) { 
        player.armorUsed = true; 
        createExplosion(player.x+17, player.y, "#4af"); 
    } else { 
        player.alive = false;
        createExplosion(player.x + 17, player.y + 17, "#0f0", 50); 
        createExplosion(player.x + 17, player.y + 17, "orange", 30);
        boosters.armor = false; boosters.doubleDamage = false; boosters.slowEnemies = false;
        setTimeout(() => { 
            gameOver = true; 
            if(score > highscore) { highscore = Math.floor(score); } 
            saveProgress(); 
            updateUI(); 
        }, 1000);
    }
}

function update(sf) {
    if (rainbowTimer > 0) rainbowTimer -= 1 * sf;
    particles.forEach((p, i) => { p.x += p.vx * sf; p.y += p.vy * sf; p.life -= 0.02 * sf; if(p.life <= 0) particles.splice(i,1); });
    floatingTexts.forEach((t, i) => { t.y -= 1 * sf; t.life -= 0.02 * sf; if(t.life <= 0) floatingTexts.splice(i,1); });
    
    if (gameOver || paused) return;

    if (player.alive) score += 0.5 * sf;
    
    if (score >= 50000 && !bossSpawned) {
        spawnBoss();
    }

    if (player.alive && activeWeapon !== "none") {
    if (shootCooldown <= 0) {
        fire();
    }
}

if (shootCooldown > 0) shootCooldown -= 1 * sf;
    
    stars.forEach(s => { s.y += s.s * sf; if(s.y > 600) s.y = 0; });
    
    if (player.alive) {
        if ((keys['a'] || keys['arrowleft']) && player.x > 0) player.x -= player.speed * sf;
        if ((keys['d'] || keys['arrowright']) && player.x < 400 - player.width) player.x += player.speed * sf;
    }
    
    bullets.forEach((b, i) => {
        b.x += b.vx * sf; b.y += b.vy * sf;
        if (b.y < -20 || b.x < -20 || b.x > 420) bullets.splice(i, 1);
    });

    enemyBullets.forEach((eb, i) => {
        eb.y += eb.speedY * sf;
        if (eb.y > 620) enemyBullets.splice(i, 1);
        
        if (player.alive && eb.x > player.x && eb.x < player.x + player.width && eb.y > player.y && eb.y < player.y + player.height) {
            enemyBullets.splice(i, 1);
            killPlayer();
        }
    });

    let enemySpeedMult = (boosters.slowEnemies ? 0.5 : 1.0) * sf;

    // --- BOSS OPPFØRSEL ---
    if (activeBoss) {
        if (activeBoss.y < activeBoss.targetY) {
            activeBoss.y += 1 * sf;
            activeBoss.centerX = activeBoss.x;
        } else {
            activeBoss.angle += 0.02 * enemySpeedMult;
            activeBoss.x = 140 + Math.sin(activeBoss.angle) * 110;
        }

        activeBoss.shootTimer += 1 * sf;
        if (activeBoss.shootTimer > 70) { 
            enemyBullets.push({ x: activeBoss.x + 20, y: activeBoss.y + 70, speedY: 4 });
            enemyBullets.push({ x: activeBoss.x + activeBoss.w - 20, y: activeBoss.y + 70, speedY: 4 });
            activeBoss.shootTimer = 0;
        }

        if (player.alive && player.x < activeBoss.x + activeBoss.w && player.x + player.width > activeBoss.x && player.y < activeBoss.y + activeBoss.h && player.y + player.height > activeBoss.y) {
            killPlayer();
        }

        bullets.forEach((b, bi) => {
            // Sjekk om activeBoss fortsatt eksisterer for å hindre krasj under fjerning av kuler
            if (activeBoss && b.x < activeBoss.x + activeBoss.w && b.x + 6 > activeBoss.x && b.y < activeBoss.y + activeBoss.h && b.y + 12 > activeBoss.y) {
                activeBoss.hp -= b.dmg;
                bullets.splice(bi, 1);
                createExplosion(b.x, b.y, "orange", 3);

                if (activeBoss.hp <= 0) {
                    createExplosion(activeBoss.x + activeBoss.w/2, activeBoss.y + activeBoss.h/2, "red", 100);
                    createExplosion(activeBoss.x + activeBoss.w/2, activeBoss.y + activeBoss.h/2, "gold", 60);
                    
                    coins += 8000;
                    gems += 150;
                    voidOverlordOwned = true;
                    weaponsOwned.sniper = true;
                    activeWeapon = "sniper"; // Bytter automatisk til den nye sniperen!
                    
                    floatingTexts.push({x: 100, y: 300, text: "DREADNOUGHT DETONATED!", color: "#f00", life: 3});
                    floatingTexts.push({x: 100, y: 330, text: "+8000 COINS! +150 GEMS!", color: "#ff0", life: 3});
                    floatingTexts.push({x: 100, y: 360, text: "NEW WEAPON & SKIN UNLOCKED!", color: "#a0f", life: 3});
                    
                    activeBoss = null; 
                    saveProgress();
                    updateUI();
                }
            }
        });
    }
    
    // Oppdater vanlige fiender
    enemies.forEach((e, ei) => {
        if (e.type === 'sinus') {
            if (!e.centerX) e.centerX = e.x;
            e.angle += 0.05 * sf; e.x = e.centerX + Math.sin(e.angle) * 50;
            e.y += e.speedY * enemySpeedMult;
        } else { e.y += e.speedY * enemySpeedMult; }

        if (player.alive && player.x < e.x + e.w && player.x + player.width > e.x && player.y < e.y + e.h && player.y + player.height > e.y) {
            if (rainbowTimer > 0) {
                enemies.splice(ei, 1);
                createExplosion(e.x + e.w/2, e.y + e.h/2, "white", 10);
                score += (e.isHeavy ? 500 : 100); coins += (e.coins || 10); updateUI();
            } else { 
                enemies.splice(ei, 1);
                killPlayer(); 
            }
        }

        bullets.forEach((b, bi) => {
            if (b.x < e.x + e.w && b.x + 6 > e.x && b.y < e.y + e.h && b.y + 12 > e.y) {
                e.hp -= b.dmg; bullets.splice(bi, 1);
                if (e.hp <= 0) {
                    if (Math.random() < 0.05) { gems += 5; floatingTexts.push({x: e.x, y: e.y, text: "+5 GEMS!", color: "#a4f", life: 1}); }
                    coins += (e.coins || 10); score += (e.isHeavy ? 500 : 100); 
                    createExplosion(e.x+e.w/2, e.y+e.h/2, e.color);
                    if (Math.random() < 0.01) powerups.push({ x: e.x, y: e.y, w: 25, h: 25, speedY: 2 });
                    enemies.splice(ei, 1); updateUI();
                }
            }   
        });
    }); 

    powerups.forEach((p, pi) => {
        p.y += p.speedY * sf;
        if (player.alive && player.x < p.x + p.w && player.x + player.width > p.x && player.y < p.y + p.h && player.y + player.height > p.y) {
            rainbowTimer = 500; powerups.splice(pi, 1);
            floatingTexts.push({x: player.x, y: player.y - 20, text: "ULTRA RAINBOW!", color: "#f0f", life: 2});
        }
        if (p.y > 600) powerups.splice(pi, 1);
    });
}

function draw() {
    ctx.clearRect(0,0,400,600);
    stars.forEach(s => { ctx.fillStyle='white'; ctx.fillRect(s.x,s.y,2,2); });
    particles.forEach(p => { ctx.globalAlpha = p.life; ctx.fillStyle = p.color; ctx.fillRect(p.x, p.y, 4, 4); });
    floatingTexts.forEach(t => { ctx.globalAlpha = t.life; ctx.fillStyle = t.color; ctx.font="bold 14px Arial"; ctx.fillText(t.text, t.x, t.y); });
    ctx.globalAlpha = 1;
    
    if (player.alive) {
        if (currentSkin === "Void Overlord") {
            let time = Date.now();
            let pulse = Math.sin(time * 0.005) * 5;
            
            // 1. Ekstern kosmisk aura (Nebula)
            ctx.shadowBlur = 20 + pulse;
            ctx.shadowColor = "#a0f";
            
            // 2. Tegn skipets mørke skrog (Diamant-formet)
            ctx.fillStyle = "#090214";
            ctx.strokeStyle = "#d8f";
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.moveTo(player.x + player.width / 2, player.y); // Topp
            ctx.lineTo(player.x + player.width, player.y + player.height * 0.7); // Høyre vinge
            ctx.lineTo(player.x + player.width * 0.7, player.y + player.height); // Bak høyre
            ctx.lineTo(player.x + player.width * 0.3, player.y + player.height); // Bak venstre
            ctx.lineTo(player.x, player.y + player.height * 0.7); // Venstre vinge
            ctx.closePath();
            ctx.fill();
            ctx.stroke();
            ctx.shadowBlur = 0; // Nullstill skygge for indre detaljer
            
            // 3. Roterende indre ringer (Event Horizon)
            ctx.save();
            ctx.translate(player.x + player.width / 2, player.y + player.height / 2);
            ctx.rotate(time * 0.003);
            ctx.strokeStyle = "rgba(160, 0, 255, 0.6)";
            ctx.lineWidth = 1.5;
            ctx.strokeRect(-10, -10, 20, 20);
            ctx.restore();
            
            // 4. Glødende plasma-vingetipper
            ctx.fillStyle = "#ff00aa";
            ctx.fillRect(player.x, player.y + player.height * 0.6, 3, 6);
            ctx.fillRect(player.x + player.width - 3, player.y + player.height * 0.6, 3, 6);
            
            // 5. Lysende hvit Singularitets-kjerne i midten
            ctx.fillStyle = "#fff";
            ctx.shadowBlur = 8;
            ctx.shadowColor = "#0ff";
            ctx.beginPath();
            ctx.arc(player.x + player.width / 2, player.y + player.height / 2, 4, 0, Math.PI * 2);
            ctx.fill();
            ctx.shadowBlur = 0;
        }
        else if (currentSkin === "Striker") {
            ctx.fillStyle = "#0a0a0a"; ctx.fillRect(player.x, player.y, player.width, player.height);
            ctx.strokeStyle = "#ff0"; ctx.lineWidth = 2; ctx.strokeRect(player.x, player.y, player.width, player.height);
            ctx.strokeStyle = "#ff0"; ctx.lineWidth = 1.5;
            const drawBolt = (x1, y1, x2, y2) => {
                ctx.beginPath(); ctx.moveTo(x1, y1);
                let midX = (x1 + x2) / 2 + (Math.random() - 0.5) * 10;
                let midY = (y1 + y2) / 2 + (Math.random() - 0.5) * 10;
                ctx.lineTo(midX, midY); ctx.lineTo(x2, y2); ctx.stroke();
            };
            let cx = player.x + player.width / 2, cy = player.y + player.height / 2;
            drawBolt(player.x, player.y, cx - 5, cy - 5); drawBolt(player.x + player.width, player.y, cx + 5, cy - 5); 
            drawBolt(player.x, player.y + player.height, cx - 5, cy + 5); drawBolt(player.x + player.width, player.y + player.height, cx + 5, cy + 5); 
            let pulse = Math.sin(Date.now() * 0.01) * 5; ctx.shadowBlur = 15 + pulse; ctx.shadowColor = "#ff0"; ctx.fillStyle = "#fff"; 
            ctx.beginPath(); ctx.arc(cx, cy, 4 + (Math.random() * 2), 0, Math.PI * 2); ctx.fill();
            ctx.strokeStyle = "#ff0"; ctx.lineWidth = 2; ctx.stroke(); ctx.shadowBlur = 0; 
        }
        else if (currentSkin === "The Core") {
            let hue = (Date.now() * 0.1) % 360; ctx.fillStyle = "#000"; ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`; ctx.lineWidth = 3;
            ctx.fillRect(player.x, player.y, player.width, player.height); ctx.strokeRect(player.x, player.y, player.width, player.height);
            ctx.save(); ctx.translate(player.x + player.width/2, player.y + player.height/2); ctx.rotate(Date.now() * 0.005);
            ctx.fillStyle = `hsl(${(hue+180)%360}, 100%, 50%)`; ctx.fillRect(-player.width/4, -player.height/4, player.width/2, player.height/2); ctx.restore();
        } 
        else if (currentSkin === "pig") {
            ctx.fillStyle = "#ffadad"; ctx.fillRect(player.x, player.y, player.width, player.height);
            ctx.fillStyle = "#ff85a2"; ctx.fillRect(player.x + 10, player.y + 18, 15, 10);
            ctx.fillStyle = "#d44d7d"; ctx.fillRect(player.x + 12, player.y + 21, 3, 3); ctx.fillRect(player.x + 20, player.y + 21, 3, 3);
            ctx.fillStyle = "white"; ctx.fillRect(player.x + 6, player.y + 10, 6, 6); ctx.fillRect(player.x + 23, player.y + 10, 6, 6);
            ctx.fillStyle = "black"; ctx.fillRect(player.x + 8, player.y + 12, 3, 3); ctx.fillRect(player.x + 25, player.y + 12, 3, 3);
        }
        else {
            ctx.fillStyle = (boosters.armor && !player.armorUsed) ? '#4af' : '#0f0';
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }
        if (boosters.armor && !player.armorUsed) {
            ctx.strokeStyle = "#4af"; ctx.lineWidth = 2; ctx.strokeRect(player.x - 3, player.y - 3, player.width + 6, player.height + 6);
        }
    }

    bullets.forEach(b => { 
        if (rainbowTimer > 0) { ctx.fillStyle = `hsl(${(Date.now() * 0.8) % 360}, 100%, 60%)`; } 
        else if (b.isSniper) { ctx.fillStyle = '#0ff'; ctx.shadowBlur = 10; ctx.shadowColor = '#0ff'; } 
        else { ctx.fillStyle = boosters.doubleDamage ? 'orange' : 'yellow'; }
        
        if (b.isSniper) { ctx.fillRect(b.x - 2, b.y, 10, 20); ctx.shadowBlur = 0; } 
        else { ctx.fillRect(b.x, b.y, 6, 12); }
    });

    enemyBullets.forEach(eb => {
        ctx.fillStyle = '#ff3333';
        ctx.beginPath();
        ctx.arc(eb.x, eb.y, 5, 0, Math.PI*2);
        ctx.fill();
    });

    powerups.forEach(p => {
        ctx.fillStyle = `hsl(${(Date.now() * 0.5) % 360}, 100%, 50%)`; ctx.shadowBlur = 15; ctx.shadowColor = "white";
        ctx.fillRect(p.x, p.y, p.w, p.h); ctx.strokeStyle = "white"; ctx.lineWidth = 2; ctx.strokeRect(p.x, p.y, p.w, p.h); ctx.shadowBlur = 0; 
    });

    if (activeBoss) {
        ctx.fillStyle = '#1e2124';
        ctx.fillRect(activeBoss.x, activeBoss.y, activeBoss.w, activeBoss.h);
        ctx.strokeStyle = '#3e444a';
        ctx.lineWidth = 4;
        ctx.strokeRect(activeBoss.x, activeBoss.y, activeBoss.w, activeBoss.h);
        
        ctx.fillStyle = '#111';
        ctx.fillRect(activeBoss.x + 15, activeBoss.y + 15, activeBoss.w - 30, activeBoss.h - 30);
        
        let bPulse = Math.sin(Date.now() * 0.02) * 5;
        ctx.fillStyle = '#600';
        ctx.fillRect(activeBoss.x + activeBoss.w/2 - 20, activeBoss.y + activeBoss.h/2 - 10, 40, 20);
        ctx.fillStyle = '#ff1111';
        ctx.shadowBlur = 10 + bPulse;
        ctx.shadowColor = 'red';
        ctx.fillRect(activeBoss.x + activeBoss.w/2 - 15, activeBoss.y + activeBoss.h/2 - 5, 30, 10);
        
        ctx.fillStyle = '#444';
        ctx.fillRect(activeBoss.x + 15, activeBoss.y + activeBoss.h, 15, 12);
        ctx.fillRect(activeBoss.x + activeBoss.w - 30, activeBoss.y + activeBoss.h, 15, 12);
        
        ctx.shadowBlur = 0; 

        ctx.fillStyle = "rgba(0,0,0,0.6)";
        ctx.fillRect(50, 60, 300, 16);
        ctx.fillStyle = "red";
        ctx.fillRect(52, 62, 296, 12);
        ctx.fillStyle = "lime";
        ctx.fillRect(52, 62, 296 * (activeBoss.hp / activeBoss.maxHp), 12);
        ctx.fillStyle = "white"; ctx.font = "bold 10px Arial"; ctx.textAlign = "center";
        ctx.fillText("DREADNOUGHT DESTROYER", 200, 71);
    }

    enemies.forEach(e => {
        if (e.type === 'normal') {
            ctx.fillStyle = '#2d3238'; ctx.fillRect(e.x, e.y, e.w, e.h);
            ctx.strokeStyle = '#5a626b'; ctx.lineWidth = 2; ctx.strokeRect(e.x + 1, e.y + 1, e.w - 2, e.h - 2);
            let pulse = Math.sin(Date.now() * 0.01) * 2; ctx.shadowBlur = 8 + pulse; ctx.shadowColor = '#f44'; ctx.fillStyle = '#f44';
            ctx.fillRect(e.x + e.w/2 - 3, e.y + e.h/2 - 3, 6, 6); ctx.shadowBlur = 0; 
        } 
        else if (e.type === 'sinus') {
            ctx.fillStyle = '#1a222d'; ctx.fillRect(e.x, e.y, e.w, e.h);
            ctx.strokeStyle = '#0cf'; ctx.lineWidth = 2; ctx.strokeRect(e.x + 1, e.y + 1, e.w - 2, e.h - 2);
            ctx.fillStyle = '#0cf'; ctx.fillRect(e.x + e.w/2 - 2, e.y + e.h/2 - 2, 4, 4);
        } 
        else if (e.type === 'heavy') {
            ctx.fillStyle = '#444'; ctx.fillRect(e.x, e.y, e.w, e.h);
            ctx.strokeStyle = '#666'; ctx.lineWidth = 2; ctx.strokeRect(e.x + 5, e.y + 5, e.w - 10, e.h - 10);
            ctx.fillStyle = '#f44'; ctx.fillRect(e.x + 8, e.y + 8, 6, 6); ctx.fillRect(e.x + e.w - 14, e.y + 8, 6, 6);
            ctx.fillStyle = '#333'; ctx.fillRect(e.x + e.w/2 - 2, e.y + 15, 4, e.h - 30);
        }
        if (e.isHeavy) {
            ctx.fillStyle = "red"; ctx.fillRect(e.x, e.y - 12, e.w, 6);
            ctx.fillStyle = "lime"; ctx.fillRect(e.x, e.y - 12, e.w * (e.hp/e.maxHp), 6);
        }
    });

    ctx.fillStyle = 'white'; ctx.font = 'bold 16px Arial'; ctx.textAlign = "left";
    ctx.fillText(`Score: ${Math.floor(score)}`, 10, 25);
    ctx.fillStyle = '#4af'; ctx.font = '12px Arial';
    ctx.fillText(`Highscore: ${Math.floor(highscore)}`, 10, 45);

    if(gameOver) { ctx.fillStyle="red"; ctx.font="30px Arial"; ctx.textAlign = "center"; ctx.fillText("GAME OVER", 200, 300); }
    if (paused && !gameOver) {
        ctx.fillStyle = "rgba(0, 0, 0, 0.5)"; ctx.fillRect(0, 0, 400, 600);
        ctx.fillStyle = "white"; ctx.font = "bold 40px Arial"; ctx.textAlign = "center"; ctx.fillText("PAUSED", 200, 300);
    }
} 

window.addEventListener("keydown", e => { 
    const key = e.key.toLowerCase(); keys[key] = true; 
    if (key === '1') selectWeapon('pistol'); 
    if (key === '2') selectWeapon('smg'); 
    if (key === '3') selectWeapon('shotgun'); 
    if (key === '4') selectWeapon('ar');
    if (key === '5') selectWeapon('sniper'); 
});
window.addEventListener("keyup", e => { keys[e.key.toLowerCase()] = false; });

const handleMove = (e) => {
    if(paused || gameOver || !player.alive) return;
    const rect = canvas.getBoundingClientRect();
    const clientX = e.touches ? e.touches[0].clientX : e.clientX;
    const x = (clientX - rect.left) * (canvas.width / rect.width);
    player.x = x - player.width / 2;
    if (player.x < 0) player.x = 0;
    if (player.x > canvas.width - player.width) player.x = canvas.width - player.width;
};
canvas.addEventListener("mousemove", handleMove);
canvas.addEventListener("touchmove", (e) => { e.preventDefault(); handleMove(e); }, { passive: false });

function togglePause() {
    paused = !paused;
    const pBtn = document.getElementById("pauseBtn");
    if (paused) { pBtn.innerText = "Continue"; pBtn.style.backgroundColor = "#440000"; } 
    else { pBtn.innerText = "Pause"; pBtn.style.backgroundColor = "#222"; }
}

function restartGame() { init(); }

function resetGameData() { 
    if(confirm("Vil du slette all data for denne brukeren i skyen?")) { 
        if (aktivBruker) { db.collection("users").doc(aktivBruker).delete().then(() => { location.reload(); }); }
    } 
}

let lastSpawnTime = 0;
function handleEnemySpawning(timestamp) {
    if (activeBoss) return; 
    let spawnDelay = Math.max(200, 600 - (Math.floor(score / 15000) * 50));
    if (timestamp - lastSpawnTime > spawnDelay) {
        spawnEnemy();
        let extraEnemies = Math.min(5, Math.floor(score / 20000)); 
        for (let i = 0; i < extraEnemies; i++) { spawnEnemy(); }
        lastSpawnTime = timestamp;
    }
}

function loop(timestamp) {
    let deltaTime = timestamp - lastTime;
    lastTime = timestamp;
    if (deltaTime > 100) deltaTime = 16.6; 
    let speedFactor = deltaTime / 16.6;

    update(speedFactor);
    handleEnemySpawning(timestamp); 
    draw();
    requestAnimationFrame(loop);
}

function registrerBruker() {
    const user = document.getElementById("loginUser").value.trim().toLowerCase();
    const code = document.getElementById("loginCode").value.trim();
    const msg = document.getElementById("loginMessage");
    if (!user || !code) { msg.style.color = "#ff4444"; msg.innerText = "Fyll inn både brukernavn og kode!"; return; }
    msg.style.color = "#4af"; msg.innerText = "Sjekker tilgjengelighet...";

    db.collection("users").doc(user).get().then((doc) => {
        if (doc.exists) { msg.style.color = "#ff4444"; msg.innerText = "Brukernavnet er opptatt i databasen!"; } 
        else {
            db.collection("users").doc(user).set({
                pinCode: code, coins: 0, gems: 0, highscore: 0
            }).then(() => { msg.style.color = "#0f0"; msg.innerText = "Bruker opprettet i skyen! Logg inn nå."; });
        }
    }).catch(err => { msg.style.color = "#ff4444"; msg.innerText = "Kunne ikke koble til database."; });
}

function validerInnlogging() {
    const user = document.getElementById("loginUser").value.trim().toLowerCase();
    const code = document.getElementById("loginCode").value.trim();
    const msg = document.getElementById("loginMessage");
    if (!user || !code) { msg.style.color = "#ff4444"; msg.innerText = "Fyll inn brukernavn og kode!"; return; }
    msg.style.color = "#4af"; msg.innerText = "Logger inn...";

    db.collection("users").doc(user).get().then((doc) => {
        if (doc.exists) {
            const userData = doc.data();
            if (userData.pinCode === code) {
                aktivBruker = user;
                document.getElementById("loginScreen").style.display = "none";
                lastInnBrukerData().then(() => { init(); requestAnimationFrame(loop); });
            } else { msg.style.color = "#ff4444"; msg.innerText = "Feil kode (PIN)!"; }
        } else { msg.style.color = "#ff4444"; msg.innerText = "Brukeren finnes ikke. Registrer ny først."; }
    }).catch(err => { msg.style.color = "#ff4444"; msg.innerText = "Feil under pålogging."; });
}
</script>
</body>
</html>
