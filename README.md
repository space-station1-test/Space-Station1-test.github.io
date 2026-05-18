<html lang="no">
<head>
    <meta charset="UTF-8">
    <title>Space Station - Destruction Edition</title>
    
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
            <span class="section-title">Weapons (1-4)</span>
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

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
const BASE_SPEED = 1.3;

let player, enemies, bullets, stars, particles, floatingTexts;
let powerups = []; // Ny liste for drops
let rainbowTimer = 0; // Ny timer for effekten
let score = 0, gameOver = false, paused = false, shootCooldown = 0;
let keys = {};
let uiVisible = true;
let gemMilestone = 10000;
let lastTime = 0; // For Delta Time
// SKIN LOGIKK
let currentSkin = "default";
// Sjekker om spilleren allerede eier skinnet fra før
let StrikerOwned = JSON.parse(localStorage.getItem("StrikerOwned")) || false;
let TheCoreOwned = JSON.parse(localStorage.getItem("TheCoreOwned")) || false;
let pigOwned = JSON.parse(localStorage.getItem("pigOwned")) || false;
    
function endreSkin(valg) {
    if (valg === 'Striker') {
        if (StrikerOwned) { currentSkin = 'Striker'; }
        else if (coins >= 5000) {
            coins -= 5000; StrikerOwned = true; currentSkin = 'Striker';
            localStorage.setItem("StrikerOwned", true);
        }
    } 
    else if (valg === 'The Core') { 
    if (TheCoreOwned) { // Bruk det nye navnet uten mellomrom her også
        currentSkin = 'The Core'; 
    }
    else if (coins >= 7000) {
        coins -= 7000; 
        TheCoreOwned = true; // Match variabelnavnet her
        currentSkin = 'The Core';
        localStorage.setItem("TheCoreOwned", true);
    } 
}
    // NY DEL FOR GRIS:
    else if (valg === 'pig') {
    if (pigOwned) { currentSkin = 'pig'; }
    else if (coins >= 5500) {
        coins -= 5500; pigOwned = true; currentSkin = 'pig';
        localStorage.setItem("pigOwned", true);
    } 
} 
    else { currentSkin = 'default'; }
    saveProgress();
    updateUI();
}

let coins = Number(localStorage.getItem("coins")) || 100000;
let gems = Number(localStorage.getItem("gems")) || 10000;
let highscore = Number(localStorage.getItem("highscore")) || 0;
let activeWeapon = localStorage.getItem("activeWeapon") || "none";
let weaponsOwned = JSON.parse(localStorage.getItem("weaponsOwned")) || { pistol: false, smg: false, shotgun: false, ar: false };
let weaponLevels = JSON.parse(localStorage.getItem("weaponLevels")) || { pistol: 0, smg: 0, shotgun: 0, ar: 0 };
let boosters = { armor: false, doubleDamage: false, slowEnemies: false };

const weaponConfigs = {
    pistol: { cooldown: [25, 21, 18], maxLvl: 2, type: "single", dmg: 1 },
    smg: { cooldown: [8, 5], maxLvl: 1, type: "single", dmg: 0.5 },
    shotgun: { cooldown: [45, 30], maxLvl: 1, type: "triple", dmg: 1 },
    ar: { cooldown: [14, 11], maxLvl: 1, type: "fast", dmg: 1 }
};

function toggleUI() {
    uiVisible = !uiVisible;
    
    // Skjuler innholdet i høyre meny
    document.getElementById("uiContent").classList.toggle("hidden", !uiVisible);
    
    // Skjuler hele venstre meny (skins)
    const leftUI = document.getElementById("skinShop");
    if (leftUI) {
        leftUI.classList.toggle("hidden", !uiVisible);
    }
    
    // Oppdaterer teksten på knappen
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
    upgSMG.disabled = weaponLevels.smg >= 1 || coins < 1000;

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

    ["pistol", "smg", "shotgun", "ar"].forEach(w => {
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
        } else {
            pBtn.innerText = "Buy Pig (5500🟡)";
            pBtn.disabled = coins < 5500;
        }
    }
    
    // Oppdater Neon/Core-knappen
    const tBtn = document.getElementById("coreBtn");
    if (tBtn) {
        if (TheCoreOwned) {
            tBtn.innerText = currentSkin === 'The Core' ? "The Core (Equipped)" : "Use The Core";
            tBtn.style.borderColor = "#f0f";
        } else {
            tBtn.innerText = "Buy The Core (7000🟡)";
            tBtn.disabled = coins < 7000;
        }
    }

    // Oppdater Striker-knappen
    const cBtn = document.getElementById("StrikerBtn");
    if (cBtn) {
        if (StrikerOwned) {
            cBtn.innerText = currentSkin === 'Striker' ? "Bolt (Equipped)" : "Use Bolt";
            cBtn.style.borderColor = "#0f0";
        } else {
            cBtn.innerText = "Buy Bolt (5000🟡)";
            cBtn.disabled = coins < 5000;
        }
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
        coins = 100;
        gems += 30;
        weaponLevels = { pistol: 0, smg: 0, shotgun: 0, ar: 0 };
        weaponsOwned = { pistol: false, smg: false, shotgun: false, ar: false };
        boosters = { armor: false, doubleDamage: false, slowEnemies: false };
        activeWeapon = "none";
        saveProgress(); init();
    }
}

function buyBooster(type, cost) {
    if (gems >= cost && !boosters[type]) { 
        gems -= cost; boosters[type] = true; updateUI(); saveProgress(); 
    }
}

function saveProgress() {
    // Hvis ingen er logget inn ennå (f.eks. på påloggingsskjermen), avbryter vi så vi ikke overskriver noe feil
    if (!aktivBruker) return;

    localStorage.setItem(aktivBruker + "_coins", coins); 
    localStorage.setItem(aktivBruker + "_gems", gems);
    localStorage.setItem(aktivBruker + "_highscore", highscore); 
    localStorage.setItem(aktivBruker + "_activeWeapon", activeWeapon);
    localStorage.setItem(aktivBruker + "_weaponsOwned", JSON.stringify(weaponsOwned));
    localStorage.setItem(aktivBruker + "_weaponLevels", JSON.stringify(weaponLevels));
    localStorage.setItem(aktivBruker + "_currentSkin", currentSkin);
    localStorage.setItem(aktivBruker + "_StrikerOwned", StrikerOwned);
    localStorage.setItem(aktivBruker + "_TheCoreOwned", TheCoreOwned);
    localStorage.setItem(aktivBruker + "_pigOwned", pigOwned);
}

function lastInnBrukerData() {
    if (!aktivBruker) return;

    // Henter data unikt for denne brukeren, eller bruker standardverdier hvis det er en helt ny bruker
    coins = Number(localStorage.getItem(aktivBruker + "_coins")) || 0;
    gems = Number(localStorage.getItem(aktivBruker + "_gems")) || 0;
    highscore = Number(localStorage.getItem(aktivBruker + "_highscore")) || 0;
    activeWeapon = localStorage.getItem(aktivBruker + "_activeWeapon") || "none";
    
    weaponsOwned = JSON.parse(localStorage.getItem(aktivBruker + "_weaponsOwned")) || { pistol: false, smg: false, shotgun: false, ar: false };
    weaponLevels = JSON.parse(localStorage.getItem(aktivBruker + "_weaponLevels")) || { pistol: 0, smg: 0, shotgun: 0, ar: 0 };
    
    currentSkin = localStorage.getItem(aktivBruker + "_currentSkin") || "default";
    StrikerOwned = localStorage.getItem(aktivBruker + "_StrikerOwned") === "true";
    TheCoreOwned = localStorage.getItem(aktivBruker + "_TheCoreOwned") === "true";
    pigOwned = localStorage.getItem(aktivBruker + "_pigOwned") === "true";

    updateUI();
}

function init() {
    player = { x: 180, y: 540, width: 35, height: 35, speed: 7 * BASE_SPEED, armorUsed: false, alive: true };
    enemies = []; bullets = []; particles = []; floatingTexts = [];
    stars = Array.from({length:50}, () => ({x: Math.random()*400, y: Math.random()*600, s: (1+Math.random()*2) * BASE_SPEED}));
    score = 0; gemMilestone = 10000;
    gameOver = false; paused = false; shootCooldown = 0;
    updateUI();
    // Legg disse nederst i init()-funksjonen
    powerups = []; 
    rainbowTimer = 0;
}

function createExplosion(x, y, color, count = 20) {
    for (let i = 0; i < count; i++) {
        particles.push({x, y, vx: (Math.random()-0.5)*12, vy: (Math.random()-0.5)*12, life: 1, color});
    }
}

function spawnEnemy() {
    if (paused || gameOver) return;
    let extraChance = Math.min(0.2, score / 100000);
    let r = Math.random();
    if (r < 0.15 && score > 2000) {
        let hp =6 + Math.floor(score / 10000);
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
    } else {
        bullets.push({x: player.x + 15, y: player.y, vx: 0, vy: -12, dmg: damage});
    }
    shootCooldown = rainbowTimer > 0 ? config.cooldown[lvl] / 3 : config.cooldown[lvl]; // 3x raskere!
}

function update(sf) {
    if (rainbowTimer > 0) rainbowTimer -= 1 * sf;
    particles.forEach((p, i) => { p.x += p.vx * sf; p.y += p.vy * sf; p.life -= 0.02 * sf; if(p.life <= 0) particles.splice(i,1); });
    floatingTexts.forEach((t, i) => { t.y -= 1 * sf; t.life -= 0.02 * sf; if(t.life <= 0) floatingTexts.splice(i,1); });
    
    if (gameOver || paused) return;

    if (player.alive) score += 0.5 * sf;
    
    if (player.alive && activeWeapon !== "none" && shootCooldown <= 0) fire();
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

    let enemySpeedMult = (boosters.slowEnemies ? 0.5 : 1.0) * sf;
    
    enemies.forEach((e, ei) => {
        if (e.type === 'sinus') {
            if (!e.centerX) e.centerX = e.x;
            e.angle += 0.05 * sf; e.x = e.centerX + Math.sin(e.angle) * 50;
            e.y += e.speedY * enemySpeedMult;
        } else { 
            e.y += e.speedY * enemySpeedMult; 
        }

    // Spiller treffer fiende
    if (player.alive && player.x < e.x + e.w && player.x + player.width > e.x && player.y < e.y + e.h && player.y + player.height > e.y) {
    
    // SJEKK 1: Er vi i Rainbow Mode? (Da dør fienden, ikke vi)
    if (rainbowTimer > 0) {
        enemies.splice(ei, 1);
        createExplosion(e.x + e.w/2, e.y + e.h/2, "white", 10);
        score += (e.isHeavy ? 500 : 100);
        coins += (e.coins || 10);
        updateUI();
    } 
    // SJEKK 2: Har vi Armor?
    else if (boosters.armor && !player.armorUsed) { 
        player.armorUsed = true; 
        enemies.splice(ei, 1); 
        createExplosion(player.x+17, player.y, "#4af"); 
    } 
    // ELLERS: Vi dør
    else { 
        player.alive = false;
        createExplosion(player.x + 17, player.y + 17, "#0f0", 50); 
        createExplosion(player.x + 17, player.y + 17, "orange", 30);
        boosters.armor = false; boosters.doubleDamage = false; boosters.slowEnemies = false;
        setTimeout(() => { gameOver = true; if(score > highscore) { highscore = Math.floor(score); saveProgress(); } updateUI(); }, 1000);
    }
}

        // Kuler treffer fiende
        bullets.forEach((b, bi) => {
            if (b.x < e.x + e.w && b.x + 6 > e.x && b.y < e.y + e.h && b.y + 12 > e.y) {
                e.hp -= (b.dmg || 1); 
                bullets.splice(bi, 1);
                
                if (e.hp <= 0) {
                    if (Math.random() < 0.05) { 
                        gems += 5; 
                        floatingTexts.push({x: e.x, y: e.y, text: "+5 GEMS!", color: "#a4f", life: 1}); 
                    }
                    coins += (e.coins || 10); 
                    score += (e.isHeavy ? 500 : 100); 
                    createExplosion(e.x+e.w/2, e.y+e.h/2, e.color);

                    if (Math.random() < 0.01) {
                        powerups.push({ x: e.x, y: e.y, w: 25, h: 25, speedY: 2 });
                    }

                    enemies.splice(ei, 1); 
                    updateUI();
                }
            }   
        });
    }); // Denne lukker enemies.forEach

    powerups.forEach((p, pi) => {
        p.y += p.speedY * sf;
        if (player.alive && player.x < p.x + p.w && player.x + player.width > p.x && player.y < p.y + p.h && player.y + player.height > p.y) {
            rainbowTimer = 500;
            powerups.splice(pi, 1);
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
                if (currentSkin === "Striker") {// 1. HITBOX-BASE & RAMME
    ctx.fillStyle = "#0a0a0a";
    ctx.fillRect(player.x, player.y, player.width, player.height);
    
    // Gul neon-ramme
    ctx.strokeStyle = "#ff0"; 
    ctx.lineWidth = 2;
    ctx.strokeRect(player.x, player.y, player.width, player.height);

    // 2. LYN FRA HJØRNENE
    ctx.strokeStyle = "#ff0";
    ctx.lineWidth = 1.5;
    let offset = (Math.random() - 0.5) * 4; // Gjør at lynene "vibrerer" litt

    const drawBolt = (x1, y1, x2, y2) => {
        ctx.beginPath();
        ctx.moveTo(x1, y1);
        // Lager et "knekk" på lynet mot midten
        let midX = (x1 + x2) / 2 + (Math.random() - 0.5) * 10;
        let midY = (y1 + y2) / 2 + (Math.random() - 0.5) * 10;
        ctx.lineTo(midX, midY);
        ctx.lineTo(x2, y2);
        ctx.stroke();
    };

    let cx = player.x + player.width / 2;
    let cy = player.y + player.height / 2;

    // Tegner lyn fra hvert hjørne nesten inn til midten
    drawBolt(player.x, player.y, cx - 5, cy - 5); // Topp venstre
    drawBolt(player.x + player.width, player.y, cx + 5, cy - 5); // Topp høyre
    drawBolt(player.x, player.y + player.height, cx - 5, cy + 5); // Bunn venstre
    drawBolt(player.x + player.width, player.y + player.height, cx + 5, cy + 5); // Bunn høyre

        // 3. LYN-KJERNE (Senter)
    let pulse = Math.sin(Date.now() * 0.01) * 5;
    ctx.shadowBlur = 15 + pulse;
    ctx.shadowColor = "#ff0";
    ctx.fillStyle = "#fff"; 
    ctx.beginPath();
    ctx.arc(cx, cy, 4 + (Math.random() * 2), 0, Math.PI * 2);
    ctx.fill();
    
    // En gul sirkel rundt den hvite kjernen
    ctx.strokeStyle = "#ff0";
    ctx.lineWidth = 2;
    ctx.stroke();

    // VIKTIG: Nullstill gløden her så den ikke smitter over på resten av spillet!
    ctx.shadowBlur = 0; 
                }
        else if (currentSkin === "The Core") {
            // The Core design
            let hue = (Date.now() * 0.1) % 360;
            ctx.fillStyle = "#000";
            ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`;
            ctx.lineWidth = 3;
            
            ctx.fillRect(player.x, player.y, player.width, player.height);
            ctx.strokeRect(player.x, player.y, player.width, player.height);
            
            // Roterende kjerne
            ctx.save();
            ctx.translate(player.x + player.width/2, player.y + player.height/2);
            ctx.rotate(Date.now() * 0.005);
            ctx.fillStyle = `hsl(${(hue+180)%360}, 100%, 50%)`;
            ctx.fillRect(-player.width/4, -player.height/4, player.width/2, player.height/2);
            ctx.restore();
        } 
                else if (currentSkin === "pig") {
            // Kropp (Rosa)
            ctx.fillStyle = "#ffadad";
            ctx.fillRect(player.x, player.y, player.width, player.height);

            // Tryne (Mørkere rosa)
            ctx.fillStyle = "#ff85a2";
            ctx.fillRect(player.x + 10, player.y + 18, 15, 10);
            
            // Nesebor
            ctx.fillStyle = "#d44d7d";
            ctx.fillRect(player.x + 12, player.y + 21, 3, 3);
            ctx.fillRect(player.x + 20, player.y + 21, 3, 3);

            // Øyne
            ctx.fillStyle = "white";
            ctx.fillRect(player.x + 6, player.y + 10, 6, 6);
            ctx.fillRect(player.x + 23, player.y + 10, 6, 6);
            ctx.fillStyle = "black";
            ctx.fillRect(player.x + 8, player.y + 12, 3, 3);
            ctx.fillRect(player.x + 25, player.y + 12, 3, 3);
        }
        else {
            // Standard skin
            ctx.fillStyle = (boosters.armor && !player.armorUsed) ? '#4af' : '#0f0';
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }

        // Armor-effekt (blå ramme)
        if (boosters.armor && !player.armorUsed) {
            ctx.strokeStyle = "#4af";
            ctx.lineWidth = 2;
            ctx.strokeRect(player.x - 3, player.y - 3, player.width + 6, player.height + 6);
        }
    }

    
        bullets.forEach(b => { 
        if (rainbowTimer > 0) {
            // Regnbuefarge som skifter raskt (0.8 styrer farten)
            ctx.fillStyle = `hsl(${(Date.now() * 0.8) % 360}, 100%, 60%)`;
        } else {
            ctx.fillStyle = boosters.doubleDamage ? 'orange' : 'yellow'; 
        }
        ctx.fillRect(b.x, b.y, 6, 12); 
    });

    // Tegner selve den sjeldne boksen når den faller
    powerups.forEach(p => {
        ctx.fillStyle = `hsl(${(Date.now() * 0.5) % 360}, 100%, 50%)`;
        ctx.shadowBlur = 15; // Gir boksen en hvit glød så den synes godt
        ctx.shadowColor = "white";
        ctx.fillRect(p.x, p.y, p.w, p.h);
        ctx.strokeStyle = "white";
        ctx.lineWidth = 2;
        ctx.strokeRect(p.x, p.y, p.w, p.h);
        ctx.shadowBlur = 0; // Skrur av gløden for resten av grafikken
    });
enemies.forEach(e => {
        if (e.type === 'normal' || e.type === 'sinus') {
            // Tegner vanlige fiender og sinus-fiender som rene kvadrater
            ctx.fillStyle = (e.type === 'sinus') ? '#0cf' : '#f44'; // Lyseblå (#0cf) for sinus, rød (#f44) for normal
            ctx.fillRect(e.x, e.y, e.w, e.h);
        } 
        else if (e.type === 'heavy') {
            // Beholder det kule Heavy-designet ditt (Metallisk look med striper og lys)
            ctx.fillStyle = '#444';
            ctx.fillRect(e.x, e.y, e.w, e.h);
            ctx.strokeStyle = '#666';
            ctx.lineWidth = 2;
            ctx.strokeRect(e.x + 5, e.y + 5, e.w - 10, e.h - 10);
            
            ctx.fillStyle = '#f44'; 
            ctx.fillRect(e.x + 8, e.y + 8, 6, 6);
            ctx.fillRect(e.x + e.w - 14, e.y + 8, 6, 6);
            
            ctx.fillStyle = '#333';
            ctx.fillRect(e.x + e.w/2 - 2, e.y + 15, 4, e.h - 30);
        }

        // HP-bar for Heavy
        if (e.isHeavy) {
            ctx.fillStyle = "red"; 
            ctx.fillRect(e.x, e.y - 12, e.w, 6);
            ctx.fillStyle = "lime"; 
            ctx.fillRect(e.x, e.y - 12, e.w * (e.hp/e.maxHp), 6);
        }
    });

    // UI og tekst
    ctx.fillStyle = 'white'; ctx.font = 'bold 16px Arial';
    ctx.textAlign = "left";
    ctx.fillText(`Score: ${Math.floor(score)}`, 10, 25);
    ctx.fillStyle = '#4af'; ctx.font = '12px Arial';
    ctx.fillText(`Highscore: ${Math.floor(highscore)}`, 10, 45);

    if(gameOver) { 
        ctx.fillStyle="red"; ctx.font="30px Arial"; ctx.textAlign = "center";
        ctx.fillText("GAME OVER", 200, 300); 
    }

    if (paused && !gameOver) {
        ctx.fillStyle = "rgba(0, 0, 0, 0.5)";
        ctx.fillRect(0, 0, 400, 600);
        ctx.fillStyle = "white";
        ctx.font = "bold 40px Arial";
        ctx.textAlign = "center";
        ctx.fillText("PAUSED", 200, 300);
    }
} // LUKKER DRAW-FUNKSJONEN

window.addEventListener("keydown", e => { 
    const key = e.key.toLowerCase(); keys[key] = true; 
    if (key === '1') selectWeapon('pistol'); 
    if (key === '2') selectWeapon('smg'); 
    if (key === '3') selectWeapon('shotgun'); 
    if (key === '4') selectWeapon('ar');
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
    
    if (paused) {
        pBtn.innerText = "Continue"; // Teksten når spillet ER pauset
        pBtn.style.backgroundColor = "#440000"; // Valgfritt: gjør knappen rødlig når pauset
    } else {
        pBtn.innerText = "Pause"; // Teksten når spillet kjører
        pBtn.style.backgroundColor = "#222"; // Tilbake til vanlig farge
    }
}

function restartGame() { init(); }
function resetGameData() { if(confirm("Delete all data?")) { localStorage.clear(); location.reload(); } }

init();
let lastSpawnTime = 0;
function handleEnemySpawning(timestamp) {
    // Øker tiden mellom hver vanlige spawn litt mer gradvis
    let spawnDelay = Math.max(200, 600 - (Math.floor(score / 15000) * 50));

    if (timestamp - lastSpawnTime > spawnDelay) {
        spawnEnemy();
        
        // NY LOGIKK: Spawner færre ekstra fiender. 
        // Her spawner vi 1 ekstra fiende per 20 000 poeng, 
        // og vi setter en maksgrense på 3 ekstra fiender samtidig.
        let extraEnemies = Math.min(5, Math.floor(score / 20000)); 
        
        for (let i = 0; i < extraEnemies; i++) {
            spawnEnemy();
        }
        
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

// --- PÅLOGGINGSLOGIKK SOM STARTER SPILLET ---
let lagredeBrukere = JSON.parse(localStorage.getItem("gameUsers")) || {};
let aktivBruker = null;

function registrerBruker() {
    const user = document.getElementById("loginUser").value.trim();
    const code = document.getElementById("loginCode").value.trim();
    const msg = document.getElementById("loginMessage");

    if (!user || !code) {
        msg.style.color = "#ff4444";
        msg.innerText = "Fyll inn både brukernavn og kode!";
        return;
    }

    if (lagredeBrukere[user]) {
        msg.style.color = "#ff4444";
        msg.innerText = "Brukernavnet er opptatt!";
    } else {
        lagredeBrukere[user] = code;
        localStorage.setItem("gameUsers", JSON.stringify(lagredeBrukere));
        msg.style.color = "#0f0";
        msg.innerText = "Bruker opprettet! Klikk Logg inn.";
    }
}

function validerInnlogging() {
    const user = document.getElementById("loginUser").value.trim();
    const code = document.getElementById("loginCode").value.trim();
    const msg = document.getElementById("loginMessage");

    if (lagredeBrukere[user] && lagredeBrukere[user] === code) {
        aktivBruker = user;
        document.getElementById("loginScreen").style.display = "none";
        
        // --- NYTT: Laster inn denne spesifikke brukeren sine lagrede ting før spillet starter ---
        lastInnBrukerData();
        
        init();
        requestAnimationFrame(loop);
    } else {
        msg.style.color = "#ff4444";
        msg.innerText = "Feil brukernavn eller kode!";
    }
}

// Merk: init() og requestAnimationFrame(loop) kjøres IKKE her lenger.
// De kjøres nå bare inni validerInnlogging() når koden er riktig!
</script>
</body>
</html>
