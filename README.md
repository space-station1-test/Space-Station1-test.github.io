<html lang="no">
<head>
<meta charset="UTF-8">
<title>Space Station - Creeper Edition</title>
<style>
    body { margin:0; background:black; color:white; display:flex; justify-content:center; align-items:center; height:100vh; font-family:Arial; overflow:hidden; touch-action: none; }
    canvas { background:#05080f; border:2px solid #4af; max-width: 100vw; max-height: 100vh; cursor: crosshair; }
    .ui { position:absolute; top:10px; right:10px; display:flex; flex-direction:column; gap:5px; z-index:10; width: 195px; background: rgba(0,0,0,0.85); padding: 10px; border-radius: 8px; border: 1px solid #4af; max-height: 90vh; overflow-y: auto; }
    button { padding:8px; font-size:11px; cursor:pointer; background: #222; color: white; border: 1px solid #4af; border-radius: 4px; width: 100%; margin-bottom: 2px; }
    button:active { background: #4af; }
    .hidden { display: none !important; }
    .section-title { font-size: 10px; color: #4af; font-weight: bold; text-transform: uppercase; margin-top: 10px; display: block; }
</style>
</head>
<body>

<div class="ui">
    <div id="shop">
        <span class="section-title">Velg Skin</span>
        <button onclick="endreSkin('creeper')">Bruk Creeper 🟩</button>
        <button onclick="endreSkin('default')">Standard 🚀</button>
    </div>
    <button onclick="location.reload()" style="margin-top:10px; border-color:red;">Restart Alt</button>
</div>

<canvas id="game" width="400" height="600"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let player = { x: 180, y: 540, width: 35, height: 35, alive: true };
let currentSkin = "default";

// HER ER DEN NYE BILDELASTINGEN
const creeperImg = new Image();
// Dette er en høykvalitets Base64 av et Creeper-ansikt
creeperImg.src = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAALHRFWHRDcmVhdGlvbiBUaW1lAFN1biA0IE1hciAyMDEyIDIyOjM1OjI0IC0wNTAwZ7S3VAAAAB50RVh0U29mdHdhcmUAYWRvYmUgcGhvdG9zaG9wIGNzM2u23e4AAABWSURBVDhPY2RgYPhf08DAwMAAxH9BeH9V9X8Ym7mZEUj8PzL28fHByIDYv8AIBP+PhP8zMjL8RxYAsX+BEYj9C4xA7F9gBGL/AiMQ+xcYgdjMDIyMjIwMAIB2P0F/D9pXAAAAAElFTkSuQmCC";

function endreSkin(valg) {
    currentSkin = valg;
    console.log("Skin valgt:", valg);
}

function draw() {
    ctx.clearRect(0, 0, 400, 600);

    if (player.alive) {
        if (currentSkin === "creeper") {
            // Sjekk om bildet faktisk har innhold
            if (creeperImg.complete && creeperImg.naturalWidth > 0) {
                ctx.drawImage(creeperImg, player.x, player.y, player.width, player.height);
            } else {
                // HVIS BILDET SVIKTER: Tegn en "Creeper" med kode
                ctx.fillStyle = "#2e8b57"; // Mørkegrønn
                ctx.fillRect(player.x, player.y, player.width, player.height);
                ctx.fillStyle = "black";
                ctx.fillRect(player.x + 5, player.y + 8, 8, 8); // Venstre øye
                ctx.fillRect(player.x + 22, player.y + 8, 8, 8); // Høyre øye
                ctx.fillRect(player.x + 12, player.y + 16, 11, 11); // Munn
            }
        } else {
            // Standard skin
            ctx.fillStyle = "#0f0";
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }
    }

    requestAnimationFrame(draw);
}

// Enkel styring for test
window.addEventListener("mousemove", (e) => {
    const rect = canvas.getBoundingClientRect();
    player.x = (e.clientX - rect.left) - player.width / 2;
});

draw();
</script>
</body>
</html>
