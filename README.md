<html lang="no">
<head>
    <meta charset="UTF-8">
    <title>Space Station - Destruction Edition</title>
    <style>
        body { margin:0; background:black; color:white; display:flex; justify-content:center; align-items:center; height:100vh; font-family:Arial; overflow:hidden; touch-action: none; }
        canvas { background:#05080f; border:2px solid #4af; max-width: 100vw; max-height: 100vh; cursor: crosshair; }
        .ui { position:absolute; top:10px; right:10px; display:flex; flex-direction:column; gap:5px; z-index:10; width: 195px; background: rgba(0,0,0,0.85); padding: 10px; border-radius: 8px; border: 1px solid #4af; max-height: 90vh; overflow-y: auto; }
        button { padding:8px; font-size:11px; cursor:pointer; background: #222; color: white; border: 1px solid #4af; border-radius: 4px; width: 100%; margin-bottom: 2px; }
        button:active { background: #4af; }
        .hidden { display: none !important; }
    </style>
</head>
<body>

<div class="ui" id="mainUI">
    <button id="toggleUIBtn" onclick="toggleUI()">Hide UI</button>
    <div id="uiContent">
        <div id="shop">
            <span class="section-title">Skins</span>
            <button onclick="endreSkin('creeper')">Bruk Creeper 🟩</button>
            <button onclick="endreSkin('default')">Standard 🚀</button>
        </div>
    </div>
</div>

<canvas id="game" width="400" height="600"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
let player = { x: 180, y: 540, width: 35, height: 35, alive: true };
let currentSkin = "default";
const creeperImg = new Image();
creeperImg.crossOrigin = "anonymous";
creeperImg.src = "https://minecraftfaces.com/wp-content/themes/minecraftfaces/faces/creeper-face.png";

// HER ER DEN NYE FUNKSJONEN
function endreSkin(valg) {
    currentSkin = valg;
}

function toggleUI() {
    document.getElementById("uiContent").classList.toggle("hidden");
}

function draw() {
    ctx.clearRect(0,0,400,600);
    
    if (player.alive) {
        // Logikken for å tegne skin
        if (currentSkin === "creeper" && creeperImg.complete && creeperImg.naturalWidth > 0) {
            ctx.drawImage(creeperImg, player.x, player.y, player.width, player.height);
        } else {
            ctx.fillStyle = "#0f0";
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }
    }
    requestAnimationFrame(draw);
}
draw();
</script>
</body>
</html>
