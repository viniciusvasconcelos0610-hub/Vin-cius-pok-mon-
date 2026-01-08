# Vin-cius-pok-mon-
Jogo de Pokémon teste1app 
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; } 
        body { background: #111; display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; margin: 0; font-family: 'Courier New', monospace; overflow: hidden; } 
        #game-container { width: 320px; height: 240px; background: #78c850; position: relative; border: 4px solid #333; overflow: hidden; image-rendering: pixelated; } 
        .screen { display: none; width: 100%; height: 100%; position: absolute; top: 0; left: 0; } 
        .active { display: block !important; } 
        
        #world { position: absolute; width: 320px; height: 1500px; top: -1260px; transition: top 0.1s linear; }
        .pathway { position: absolute; background: #e0d0a0; width: 90px; height: 100%; left: 115px; border-left: 2px solid #c8b888; border-right: 2px solid #c8b888; } 
        .grass-patch { position: absolute; background: #50a050; width: 100px; height: 200px; border: 2px solid #387838; background-image: radial-gradient(#387838 20%, transparent 20%); background-size: 8px 8px; }

        #player { width: 18px; height: 20px; position: absolute; z-index: 50; transition: all 0.05s; }
        .p-head { width: 14px; height: 12px; background: #ffdbac; margin: 0 auto; border: 1.5px solid #000; }
        .p-body { width: 14px; height: 8px; background: #f00; margin: -1px auto; border: 1.5px solid #000; }

        .house { position: absolute; z-index: 10; border: 2px solid #333; background: #eee; }
        .roof { position: absolute; width: calc(100% + 10px); height: 20px; top: -15px; left: -5px; border: 2px solid #333; }
        .door { width: 16px; height: 18px; background: #5d4037; position: absolute; bottom: 0; left: 50%; transform: translateX(-50%); border: 1px solid #000; }
        .label { font-size: 6px; position: absolute; top: -25px; width: 100%; text-align: center; font-weight: bold; background: rgba(255,255,255,0.8); padding: 1px; border-radius: 2px; border: 1px solid #333; }

        #menu-start { position: absolute; right: 10px; top: 10px; width: 100px; background: #fff; border: 2px solid #555; z-index: 200; display: none; padding: 5px; box-shadow: 2px 2px 0 #000; }
        .modal { display: none; position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: #f8f8f8; z-index: 500; padding: 10px; overflow-y: auto; }
        
        .hud { position: absolute; background: #f8f8d8; border: 2px solid #484848; padding: 4px; width: 120px; font-size: 8px; border-radius: 0 5px 0 5px; }
        .hp-bar { width: 100%; height: 5px; background: #444; border: 1px solid #000; margin-top: 2px; }
        .hp-fill { height: 100%; background: #70f8a8; transition: 0.3s; }
        .exp-bar { width: 100%; height: 4px; background: #ccc; margin-top: 2px; border: 1px solid #888; }
        .exp-fill { height: 100%; background: #42a5f5; width: 0%; transition: 0.8s ease-out; }

        .pkmn-line { display: flex; align-items: center; border: 2px solid #ccc; margin-bottom: 5px; background: #fff; padding: 4px; cursor: pointer; border-radius: 5px; }
        .controls { display: grid; grid-template-columns: repeat(3, 50px); gap: 10px; margin-top: 15px; }
        .btn-c { width: 50px; height: 50px; background: #333; color: #fff; border: 2px solid #eee; border-radius: 10px; font-size: 20px; }
    </style>
</head>
<body>

<div id="game-container">
    <div id="sc-name" class="screen active" style="text-align:center; padding-top:40px;">
        <p>OAK: Qual seu nome?</p>
        <input type="text" id="p-name-input" value="RED"><br><br>
        <button onclick="salvarNome()">OK</button>
        <button id="btn-load" onclick="loadGame()" style="display:none; margin-top:10px; background:#4CAF50; color:white;">CARREGAR JOGO</button>
    </div>

    <div id="sc-choice" class="screen" style="padding:10px; overflow-y:auto; background: #fff;">
        <p style="font-size:10px; text-align:center; font-weight:bold;">ESCOLHA SEU INICIAL</p>
        <div id="choice-list"></div>
    </div>

    <div id="sc-map" class="screen">
        <button onclick="toggleMenu()" style="position:absolute; top:5px; right:5px; z-index:100; font-size:8px;">START</button>
        <div id="world">
            <div class="pathway"></div>
            <div class="grass-patch" style="top:800px; left:110px;"></div>

            <div class="house" id="p-center" style="top:200px; left:30px; width:70px; height:50px;"><div class="roof" style="background:#ef5350"></div><div class="door"></div><div class="label">CENTRO POKÉMON</div></div>
            <div class="house" id="p-market" style="top:200px; left:220px; width:70px; height:50px;"><div class="roof" style="background:#42a5f5"></div><div class="door"></div><div class="label">POKÉMON MARKET</div></div>
            <div class="house" id="mom-house" style="top:1350px; left:30px; width:60px; height:50px;"><div class="roof" style="background:#d32f2f"></div><div class="door"></div><div class="label">CASA DA MÃE</div></div>
            <div class="house" id="oak-lab" style="top:1330px; left:210px; width:90px; height:70px;"><div class="roof" style="background:#90a4ae"></div><div class="door"></div><div class="label">LABORATÓRIO OAK</div></div>
            
            <div id="player"><div class="p-head"></div><div class="p-body"></div></div>
        </div>
    </div>

    <div id="sc-battle" class="screen" style="background: #e0e0e0;">
        <div class="hud" style="top:10px; left:10px;"><span id="e-name"></span> Lv5<div class="hp-bar"><div id="ehp" class="hp-fill"></div></div></div>
        <div id="e-img" style="position:absolute; top:20px; right:30px; width:80px; height:80px; background-size:contain; background-repeat:no-repeat;"></div>
        <div class="hud" style="bottom:85px; right:10px;"><span id="p-name-b"></span> Lv<span id="p-lv-b"></span>
            <div class="hp-bar"><div id="php" class="hp-fill"></div></div>
            <div class="exp-bar"><div id="pexp" class="exp-fill"></div></div>
        </div>
        <div id="p-img" style="position:absolute; bottom:80px; left:20px; width:90px; height:90px; background-size:contain; background-repeat:no-repeat;"></div>
        <div style="position:absolute; bottom:0; width:100%; height:75px; background:#333; display:grid; grid-template-columns: 1.2fr 1fr;">
            <div id="b-msg" style="color:white; font-size:9px; padding:10px;">O que fazer?</div>
            <div style="display:grid; grid-template-columns: 1fr 1fr; background:#fff;">
                <button onclick="abrirAtaques()">LUTAR</button>
                <button onclick="abrirBolsa(true)">BAG</button>
                <button onclick="abrirEquipe(true)">PKMN</button>
                <button onclick="fecharBatalha()">FUGIR</button>
            </div>
        </div>
        <div id="move-menu" style="display:none; position:absolute; bottom:0; width:100%; height:75px; background:white; grid-template-columns:1fr 1fr; z-index:100;"></div>
    </div>

    <div id="menu-start">
        <button style="width:100%; margin-bottom:5px;" onclick="abrirEquipe(false)">POKÉMON</button>
        <button style="width:100%; margin-bottom:5px;" onclick="abrirBolsa(false)">BAG</button>
        <button style="width:100%; margin-bottom:5px;" onclick="saveGame()">SALVAR</button>
        <button style="width:100%" onclick="toggleMenu()">SAIR</button>
    </div>
    <div id="modal" class="modal"><div id="m-content"></div><button onclick="fecharModal()" style="width:100%; margin-top:10px; height:35px;">VOLTAR</button></div>
</div>

<div class="controls">
    <div></div><button class="btn-c" onclick="mover('u')">▲</button><div></div>
    <button class="btn-c" onclick="mover('l')">◀</button><button class="btn-c" onclick="mover('d')">▼</button><button class="btn-c" onclick="mover('r')">▶</button>
</div>

<script>
let player = { n: "RED", x: 150, y: 1400, balls: 0, pkmn: [], ativo: 0, encomenda: false };
let inimigo = null, emBatalha = false, turno = false;

// LISTA ORGANIZADA: Pikachu -> Kanto -> Johto -> Hoenn -> Sinnoh -> Unova -> Kalos
const pData = [
    {id:25, n:'Pikachu', t:'Elétrico', moves:[{n:'T-Shock',d:20},{n:'Growl',d:0},{n:'Tail Whip',d:0},{n:'Quick Atk',d:15}]},
    {id:1, n:'Bulbasaur', t:'Planta', moves:[{n:'Tackle',d:15},{n:'Growl',d:0},{n:'Vine Whip',d:20},{n:'Leech Seed',d:5}]},
    {id:4, n:'Charmander', t:'Fogo', moves:[{n:'Scratch',d:15},{n:'Growl',d:0},{n:'Ember',d:20},{n:'Smokescreen',d:0}]},
    {id:7, n:'Squirtle', t:'Água', moves:[{n:'Tackle',d:15},{n:'Tail Whip',d:0},{n:'Bubble',d:18},{n:'Withdraw',d:0}]},
    {id:152, n:'Chikorita', t:'Planta', moves:[{n:'Tackle',d:15},{n:'Growl',d:0},{n:'Razor Leaf',d:22},{n:'Poison Powd',d:5}]},
    {id:155, n:'Cyndaquil', t:'Fogo', moves:[{n:'Tackle',d:15},{n:'Leer',d:0},{n:'Ember',d:20},{n:'Smokescreen',d:0}]},
    {id:158, n:'Totodile', t:'Água', moves:[{n:'Scratch',d:15},{n:'Leer',d:0},{n:'Water Gun',d:20},{n:'Bite',d:25}]},
    {id:252, n:'Treecko', t:'Planta', moves:[{n:'Pound',d:15},{n:'Leer',d:0},{n:'Absorb',d:18},{n:'Quick Atk',d:15}]},
    {id:255, n:'Torchic', t:'Fogo', moves:[{n:'Scratch',d:15},{n:'Growl',d:0},{n:'Ember',d:20},{n:'Peck',d:18}]},
    {id:258, n:'Mudkip', t:'Água', moves:[{n:'Tackle',d:15},{n:'Growl',d:0},{n:'Water Gun',d:20},{n:'Mud-Slap',d:15}]},
    {id:387, n:'Turtwig', t:'Planta', moves:[{n:'Tackle',d:15},{n:'Withdraw',d:0},{n:'Absorb',d:18},{n:'Razor Leaf',d:22}]},
    {id:390, n:'Chimchar', t:'Fogo', moves:[{n:'Scratch',d:15},{n:'Leer',d:0},{n:'Ember',d:20},{n:'Mach Punch',d:25}]},
    {id:393, n:'Piplup', t:'Água', moves:[{n:'Pound',d:15},{n:'Growl',d:0},{n:'Bubble',d:18},{n:'Peck',d:18}]},
    {id:495, n:'Snivy', t:'Planta', moves:[{n:'Tackle',d:15},{n:'Leer',d:0},{n:'Vine Whip',d:20},{n:'Wrap',d:15}]},
    {id:498, n:'Tepig', t:'Fogo', moves:[{n:'Tackle',d:15},{n:'Tail Whip',d:0},{n:'Ember',d:20},{n:'Defense Curl',d:0}]},
    {id:501, n:'Oshawott', t:'Água', moves:[{n:'Tackle',d:15},{n:'Tail Whip',d:0},{n:'Water Gun',d:20},{n:'Water Sport',d:0}]},
    {id:650, n:'Chespin', t:'Planta', moves:[{n:'Tackle',d:15},{n:'Growl',d:0},{n:'Vine Whip',d:20},{n:'Rollout',d:25}]},
    {id:653, n:'Fennekin', t:'Fogo', moves:[{n:'Scratch',d:15},{n:'Tail Whip',d:0},{n:'Ember',d:20},{n:'Psybeam',d:25}]},
    {id:656, n:'Froakie', t:'Água', moves:[{n:'Pound',d:15},{n:'Growl',d:0},{n:'Bubble',d:20},{n:'Quick Atk',d:15}]}
];

function salvarNome() {
    player.n = document.getElementById('p-name-input').value.toUpperCase();
    let list = document.getElementById('choice-list');
    list.innerHTML = "";
    pData.forEach(p => {
        list.innerHTML += `<div class="pkmn-line" onclick="escolher(${p.id})">
            <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${p.id}.png" width="40">
            <span><b>${p.n}</b></span>
        </div>`;
    });
    mudar('sc-choice');
}

function escolher(id) {
    let p = pData.find(x => x.id == id);
    player.pkmn.push({ ...p, lv: 5, hp: 50, mhp: 50, exp: 0 });
    mudar('sc-map'); atualizarPos();
}

function mover(dir) {
    if(emBatalha) return;
    if(dir=='u') player.y-=15; if(dir=='d') player.y+=15;
    if(dir=='l') player.x-=15; if(dir=='r') player.x+=15;
    
    // CASA DA MÃE
    if(player.y > 1340 && player.x < 100) {
        player.pkmn.forEach(p => p.hp = p.mhp); 
        alert("MÃE: Seus Pokémon estão descansados!"); 
        player.y -= 30;
    }
    // CENTRO POKÉMON (Cura)
    if(player.y < 260 && player.y > 170 && player.x < 110) {
        player.pkmn.forEach(p => p.hp = p.mhp); 
        alert("Enfermeira Joy: Curamos seus Pokémon!"); 
        player.y += 40;
    }
    // MARKET
    if(player.y < 260 && player.y > 170 && player.x > 210) {
        if(!player.encomenda && player.balls == 0) { 
            player.encomenda = true; alert("Pegou a Encomenda para Oak!"); player.y += 40; 
        }
    }
    // LABORATÓRIO
    if(player.y > 1300 && player.x > 180 && player.encomenda) {
        player.encomenda = false; player.balls = 10; 
        alert("OAK: Obrigado! Aqui estão 10 Pokébolas!"); player.y -= 40;
    }

    if(player.y < 1000 && player.y > 750 && Math.random() < 0.12) entrarBatalha();
    atualizarPos();
}

function vencer() {
    let p = player.pkmn[player.ativo];
    p.exp += 75;
    msgBatalha("Venceu! " + p.n + " ganhou XP!");
    
    setTimeout(() => {
        // Mostra XP subindo
        document.getElementById('pexp').style.width = (p.exp > 100 ? 100 : p.exp) + "%";
        
        setTimeout(() => {
            if(p.exp >= 100) {
                p.lv++; p.exp = 0; p.mhp += 5; p.hp = p.mhp;
                alert(p.n + " subiu para o Lv " + p.lv + "!");
            }
            fecharBatalha();
        }, 1000);
    }, 500);
}

function abrirEquipe(bat) {
    document.getElementById('modal').style.display = 'block';
    let c = document.getElementById('m-content');
    c.innerHTML = "<h4>EQUIPE</h4>";
    player.pkmn.forEach((p, i) => {
        c.innerHTML += `<div class="pkmn-line" onclick="trocar(${i}, ${bat})">
            <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${p.id}.png" width="35">
            <div style="font-size:8px; flex-grow:1; margin-left:10px;">
                <b>${p.n}</b> Lv${p.lv}<br>
                <div class="hp-bar"><div class="hp-fill" style="width:${(p.hp/p.mhp*100)}%"></div></div>
                HP: ${p.hp}/${p.mhp}
            </div>
        </div>`;
    });
}

function abrirBolsa(bat) {
    document.getElementById('modal').style.display = 'block';
    document.getElementById('m-content').innerHTML = `
        <h4>BOLSA</h4>
        <div class="pkmn-line" onclick="usarBola(${bat})">
            <span style="font-size:18px; margin-right:10px;">🔴</span>
            <span><b>Pokébolas</b> x${player.balls}</span>
        </div>`;
}

function usarBola(b) {
    if(!b || player.balls <= 0) return;
    player.balls--; fecharModal(); 
    msgBatalha("Lançou 🔴 Pokébola..."); 
    setTimeout(() => { 
        if(Math.random() > 0.5) { 
            alert("Capturou " + inimigo.n + "!"); 
            player.pkmn.push({...inimigo, exp:0}); 
            fecharBatalha(); 
        } else { msgBatalha("Escapou!"); } 
    }, 1000);
}

function saveGame() {
    localStorage.setItem('pokeSave_vFinal', JSON.stringify(player));
    alert("Jogo Salvo!");
    toggleMenu();
}

function loadGame() {
    let save = localStorage.getItem('pokeSave_vFinal');
    if(save) {
        player = JSON.parse(save);
        mudar('sc-map'); atualizarPos();
    }
}

if(localStorage.getItem('pokeSave_vFinal')) document.getElementById('btn-load').style.display = 'inline-block';

function atacar(d, n) {
    if(turno) return; turno = true; document.getElementById('move-menu').style.display = 'none';
    msgBatalha(`${player.pkmn[player.ativo].n} usou ${n}!`);
    setTimeout(() => {
        inimigo.hp -= d; atualizarBatalha();
        if(inimigo.hp <= 0) vencer();
        else setTimeout(() => {
            player.pkmn[player.ativo].hp -= 7; atualizarBatalha();
            msgBatalha(`${inimigo.n} selvagem ataca!`);
            if(player.pkmn[player.ativo].hp <= 0) alert("Pokémon desmaiou!");
            turno = false;
        }, 1000);
    }, 1000);
}
function abrirAtaques() {
    let m = document.getElementById('move-menu'); m.style.display = 'grid'; m.innerHTML = '';
    player.pkmn[player.ativo].moves.forEach(a => m.innerHTML += `<button onclick="atacar(${a.d},'${a.n}')">${a.n}</button>`);
}
function trocar(i, bat) { player.ativo = i; fecharModal(); if(bat) atualizarBatalha(); }
function entrarBatalha() { inimigo = {...pData[Math.floor(Math.random()*pData.length)], lv:5, hp:40, mhp:40}; emBatalha = true; mudar('sc-battle'); atualizarBatalha(); }
function atualizarBatalha() {
    let p = player.pkmn[player.ativo];
    document.getElementById('p-name-b').innerText = p.n; document.getElementById('p-lv-b').innerText = p.lv;
    document.getElementById('php').style.width = (p.hp/p.mhp*100)+"%";
    document.getElementById('pexp').style.width = (p.exp)+"%";
    document.getElementById('p-img').style.backgroundImage = `url('https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/back/${p.id}.png')`;
    document.getElementById('e-name').innerText = inimigo.n;
    document.getElementById('ehp').style.width = (inimigo.hp/inimigo.mhp*100)+"%";
    document.getElementById('e-img').style.backgroundImage = `url('https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${inimigo.id}.png')`;
}
function atualizarPos() { document.getElementById('player').style.top = player.y+"px"; document.getElementById('player').style.left = player.x+"px"; document.getElementById('world').style.top = -(player.y-100)+"px"; }
function msgBatalha(t) { document.getElementById('b-msg').innerText = t; }
function fecharBatalha() { emBatalha = false; mudar('sc-map'); turno = false; }
function fecharModal() { document.getElementById('modal').style.display = 'none'; }
function toggleMenu() { let m = document.getElementById('menu-start'); m.style.display = m.style.display == 'block' ? 'none' : 'block'; }
function mudar(id) { document.querySelectorAll('.screen').forEach(s => s.classList.remove('active')); document.getElementById(id).classList.add('active'); }
</script>
</body>
</html>
