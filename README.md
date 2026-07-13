# CosmosDanya
 Игра «Космический сборщик энергии». Суть: игрок управляет космическим кораблём и собирает энергетические кристаллы. 
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Космический Сборщик Энергии</title>
    <style>
        *{margin:0;padding:0;box-sizing:border-box}
        :root{
            --bg:#060b18;--fg:#e8f0ff;--muted:#6a7a9a;
            --accent:#00e5ff;--gold:#ffd700;--danger:#ff3366;
            --card:rgba(8,18,40,0.92);--border:rgba(0,229,255,0.18);
        }
        body{
            background:var(--bg);color:var(--fg);
            font-family:'Segoe UI',Tahoma,Geneva,Verdana,sans-serif;
            overflow:hidden;width:100vw;height:100vh;user-select:none;
        }
        #gameCanvas{display:block;position:fixed;top:0;left:0}

        /* ======= ОВЕРЛЕИ ======= */
        .overlay{
            position:fixed;inset:0;display:flex;flex-direction:column;
            align-items:center;justify-content:center;z-index:10;
            background:radial-gradient(ellipse at 50% 40%,rgba(0,50,80,0.25) 0%,rgba(6,11,24,0.96) 70%);
            transition:opacity .45s ease;
        }
        .overlay.hidden{opacity:0;pointer-events:none}

        .game-title{
            font-size:clamp(1.8rem,5.5vw,3.6rem);font-weight:900;
            letter-spacing:.04em;text-align:center;line-height:1.15;
            background:linear-gradient(135deg,var(--accent) 0%,var(--gold) 100%);
            -webkit-background-clip:text;-webkit-text-fill-color:transparent;
            background-clip:text;margin-bottom:.6rem;
        }
        .game-subtitle{
            font-size:clamp(.85rem,2vw,1.15rem);color:var(--muted);
            margin-bottom:2.2rem;text-align:center;max-width:420px;line-height:1.65;
        }

        .rules-box{
            background:var(--card);border:1px solid var(--border);
            border-radius:16px;padding:1.4rem 1.8rem;margin-bottom:2rem;
            max-width:400px;width:90%;backdrop-filter:blur(12px);
        }
        .rules-box h3{color:var(--accent);font-size:1rem;margin-bottom:.7rem}
        .rules-box ul{list-style:none;padding:0}
        .rules-box li{
            padding:.35rem 0;color:var(--fg);font-size:.92rem;
            display:flex;align-items:center;gap:.6rem;
        }
        .rules-box li .icon{font-size:1.2rem;width:28px;text-align:center;flex-shrink:0}

        .btn{
            padding:.9rem 2.8rem;font-size:1.15rem;font-weight:700;
            border:none;border-radius:50px;cursor:pointer;
            transition:all .25s ease;letter-spacing:.03em;outline:none;
        }
        .btn:focus-visible{box-shadow:0 0 0 3px rgba(0,229,255,.5)}
        .btn-primary{
            background:linear-gradient(135deg,var(--accent),#00b8d4);color:var(--bg);
            box-shadow:0 4px 24px rgba(0,229,255,.35);
        }
        .btn-primary:hover{transform:translateY(-2px);box-shadow:0 8px 32px rgba(0,229,255,.55)}
        .btn-primary:active{transform:translateY(0)}

        .controls-hint{
            margin-top:1.4rem;color:var(--muted);font-size:.82rem;text-align:center;
        }
        .controls-hint kbd{
            background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.15);
            border-radius:4px;padding:.1rem .45rem;font-family:inherit;font-size:.8rem;
            color:var(--fg);
        }

        /* ======= КОНЕЦ ИГРЫ ======= */
        .end-title{font-size:clamp(1.4rem,4vw,2.4rem);font-weight:900;margin-bottom:.3rem}
        .end-title.win{
            background:linear-gradient(135deg,var(--gold),#ffab00);
            -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
        }
        .end-title.lose{color:var(--danger)}
        .end-reason{color:var(--muted);font-size:.95rem;margin-bottom:1.6rem}

        .stats-grid{display:grid;grid-template-columns:1fr 1fr;gap:.8rem;margin-bottom:2rem;max-width:340px;width:90%}
        .stat-card{
            background:var(--card);border:1px solid var(--border);
            border-radius:12px;padding:1rem;text-align:center;backdrop-filter:blur(8px);
        }
        .stat-value{font-size:1.8rem;font-weight:900;color:var(--accent)}
        .stat-value.gold{color:var(--gold)}
        .stat-label{font-size:.78rem;color:var(--muted);margin-top:.2rem}

        /* ======= HUD ======= */
        #hud{
            position:fixed;top:0;left:0;right:0;z-index:5;
            display:flex;justify-content:space-between;align-items:center;
            padding:.8rem 1.4rem;pointer-events:none;
            background:linear-gradient(180deg,rgba(6,11,24,.7) 0%,transparent 100%);
        }
        #hud.hidden{display:none}
        .hud-block{display:flex;align-items:center;gap:.5rem}
        .hud-label{font-size:.8rem;color:var(--muted);text-transform:uppercase;letter-spacing:.06em}
        .hud-value{font-size:1.3rem;font-weight:800}
        .hud-value.score{color:var(--gold)}
        .hud-value.timer{color:var(--accent)}
        .hud-value.timer.urgent{color:var(--danger);animation:pulse .5s ease infinite alternate}
        .hearts{display:flex;gap:.25rem;font-size:1.4rem}
        .heart{transition:all .3s ease;display:inline-block}
        .heart.lost{opacity:.2;transform:scale(.7);filter:grayscale(1)}

        @keyframes pulse{0%{opacity:1}100%{opacity:.5}}
        @keyframes float-in{from{opacity:0;transform:translateY(20px)}to{opacity:1;transform:translateY(0)}}
        .float-in{animation:float-in .5s ease both}
        .float-in-d1{animation-delay:.1s}
        .float-in-d2{animation-delay:.2s}
        .float-in-d3{animation-delay:.3s}
        .float-in-d4{animation-delay:.4s}

        @media(max-width:500px){
            #hud{padding:.5rem .8rem}
            .hud-value{font-size:1.1rem}
            .hearts{font-size:1.1rem}
            .rules-box{padding:1rem 1.2rem}
        }

        @media(prefers-reduced-motion:reduce){
            *{animation-duration:.01ms!important;transition-duration:.01ms!important}
        }
    </style>
</head>
<body>

<canvas id="gameCanvas"></canvas>

<!-- ======= HUD ======= -->
<div id="hud" class="hidden">
    <div class="hud-block">
        <span class="hud-label">Очки</span>
        <span class="hud-value score" id="hudScore">0</span>
    </div>
    <div class="hud-block">
        <span class="hud-label">Время</span>
        <span class="hud-value timer" id="hudTimer">60</span>
    </div>
    <div class="hud-block">
        <div class="hearts" id="hudHearts"></div>
    </div>
</div>

<!-- ======= СТАРТОВЫЙ ЭКРАН ======= -->
<div class="overlay" id="startScreen">
    <div class="game-title float-in">Космический<br>Сборщик Энергии</div>
    <p class="game-subtitle float-in float-in-d1">
        Управляй кораблём, собирай кристаллы и уклоняйся от астероидов. Успей набрать максимум очков за 60 секунд!
    </p>
    <div class="rules-box float-in float-in-d2">
        <h3>Правила игры</h3>
        <ul>
            <li><span class="icon">&#x1F48E;</span> Собирай кристаллы — каждый даёт очки</li>
            <li><span class="icon">&#x1FA90;</span> Избегай астероидов — стоит жизни</li>
            <li><span class="icon">&#x2764;&#xFE0F;</span> У тебя 3 жизни, после 0 — конец</li>
            <li><span class="icon">&#x23F1;&#xFE0F;</span> На всё про всё 60 секунд</li>
        </ul>
    </div>
    <button class="btn btn-primary float-in float-in-d3" id="btnStart">Начать игру</button>
    <p class="controls-hint float-in float-in-d4">
        Управление: <kbd>W</kbd><kbd>A</kbd><kbd>S</kbd><kbd>D</kbd> или стрелки
    </p>
</div>

<!-- ======= ЭКРАН КОНЦА ИГРЫ ======= -->
<div class="overlay hidden" id="endScreen">
    <div class="end-title" id="endTitle"></div>
    <p class="end-reason" id="endReason"></p>
    <div class="stats-grid" id="statsGrid"></div>
    <button class="btn btn-primary" id="btnRestart">Играть снова</button>
</div>

<script>
/* ============================================================
   КОСМИЧЕСКИЙ СБОРЩИК ЭНЕРГИИ — Полная игровая логика
   ============================================================ */

// ---- Элементы ----
const canvas  = document.getElementById('gameCanvas');
const ctx     = canvas.getContext('2d');
const hud     = document.getElementById('hud');
const hudScore = document.getElementById('hudScore');
const hudTimer = document.getElementById('hudTimer');
const hudHearts = document.getElementById('hudHearts');
const startScreen = document.getElementById('startScreen');
const endScreen   = document.getElementById('endScreen');
const endTitle    = document.getElementById('endTitle');
const endReason   = document.getElementById('endReason');
const statsGrid   = document.getElementById('statsGrid');
const btnStart    = document.getElementById('btnStart');
const btnRestart  = document.getElementById('btnRestart');

// ---- Константы ----
const GAME_DURATION  = 60;   // секунд
const MAX_LIVES      = 3;
const SHIP_SPEED     = 5.5;
const SHIP_SIZE      = 22;
const CRYSTAL_BASE   = 14;
const ASTEROID_MIN   = 16;
const ASTEROID_MAX   = 42;
const INVULN_TIME    = 1.8;  // секунд неуязвимости после удара
const SPAWN_INTERVAL_BASE = 900; // мс базовый интервал спавна

// Типы кристаллов: [имя, цвет, очки, вес (вероятность), размер]
const CRYSTAL_TYPES = [
    { name:'синий',   color:'#00e5ff', glow:'rgba(0,229,255,.45)',  pts:10,  w:50, sz:13 },
    { name:'зелёный', color:'#00ff88', glow:'rgba(0,255,136,.45)',  pts:25,  w:30, sz:15 },
    { name:'золотой', color:'#ffd700', glow:'rgba(255,215,0,.5)',   pts:50,  w:14, sz:17 },
    { name:'розовый', color:'#ff55aa', glow:'rgba(255,85,170,.5)',  pts:100, w:6,  sz:19 },
];

// ---- Состояние игры ----
let W, H;
let gameState = 'menu'; // menu | playing | ended
let score, lives, timeLeft, invulnTimer;
let ship, crystals, asteroids, particles, floatingTexts;
let stars = [];
let spawnAccum, lastTime;
let totalCollected, totalAsteroidsDodged, maxCombo, combo, comboTimer;
let difficultyMul;
let shakeX, shakeY, shakeDur;

// ---- Утилиты ----
function rand(a, b) { return a + Math.random() * (b - a); }
function randInt(a, b) { return Math.floor(rand(a, b + 1)); }
function dist(a, b) { return Math.hypot(a.x - b.x, a.y - b.y); }
function clamp(v, lo, hi) { return Math.max(lo, Math.min(hi, v)); }
function lerp(a, b, t) { return a + (b - a) * t; }
function weightedRandom(types) {
    let total = types.reduce((s, t) => s + t.w, 0);
    let r = Math.random() * total;
    for (let t of types) { r -= t.w; if (r <= 0) return t; }
    return types[0];
}

// ---- Звёзды фона ----
function initStars() {
    stars = [];
    for (let i = 0; i < 220; i++) {
        stars.push({
            x: rand(0, W), y: rand(0, H),
            r: rand(0.4, 2.2),
            speed: rand(0.15, 0.9),
            brightness: rand(0.3, 1),
            twinkleSpeed: rand(1.5, 4),
            twinklePhase: rand(0, Math.PI * 2)
        });
    }
}

function updateStars(dt) {
    for (let s of stars) {
        s.y += s.speed * 60 * dt;
        s.twinklePhase += s.twinkleSpeed * dt;
        if (s.y > H + 5) { s.y = -5; s.x = rand(0, W); }
    }
}

function drawStars(time) {
    for (let s of stars) {
        let alpha = s.brightness * (0.6 + 0.4 * Math.sin(s.twinklePhase));
        ctx.globalAlpha = alpha;
        ctx.fillStyle = '#c8d8ff';
        ctx.beginPath();
        ctx.arc(s.x, s.y, s.r, 0, Math.PI * 2);
        ctx.fill();
    }
    ctx.globalAlpha = 1;
}

// ---- Туманности фона ----
let nebulae = [];
function initNebulae() {
    nebulae = [];
    let colors = ['rgba(0,80,120,.06)','rgba(80,0,100,.05)','rgba(0,100,80,.04)','rgba(120,40,0,.04)'];
    for (let i = 0; i < 5; i++) {
        nebulae.push({
            x: rand(0, W), y: rand(0, H),
            r: rand(120, 300),
            color: colors[i % colors.length],
            dx: rand(-0.08, 0.08), dy: rand(0.05, 0.15)
        });
    }
}

function updateNebulae(dt) {
    for (let n of nebulae) {
        n.x += n.dx * 60 * dt;
        n.y += n.dy * 60 * dt;
        if (n.y - n.r > H) { n.y = -n.r; n.x = rand(0, W); }
        if (n.x < -n.r) n.x = W + n.r;
        if (n.x > W + n.r) n.x = -n.r;
    }
}

function drawNebulae() {
    for (let n of nebulae) {
        let g = ctx.createRadialGradient(n.x, n.y, 0, n.x, n.y, n.r);
        g.addColorStop(0, n.color);
        g.addColorStop(1, 'rgba(0,0,0,0)');
        ctx.fillStyle = g;
        ctx.fillRect(n.x - n.r, n.y - n.r, n.r * 2, n.r * 2);
    }
}

// ---- Корабль ----
function createShip() {
    return {
        x: W / 2, y: H - 80,
        vx: 0, vy: 0,
        size: SHIP_SIZE,
        enginePhase: 0
    };
}

function updateShip(dt) {
    let ax = 0, ay = 0;
    if (keys['ArrowLeft']  || keys['KeyA']) ax -= 1;
    if (keys['ArrowRight'] || keys['KeyD']) ax += 1;
    if (keys['ArrowUp']    || keys['KeyW']) ay -= 1;
    if (keys['ArrowDown']  || keys['KeyS']) ay += 1;

    // Нормализация диагонали
    let len = Math.hypot(ax, ay);
    if (len > 0) { ax /= len; ay /= len; }

    let spd = SHIP_SPEED;
    ship.vx = lerp(ship.vx, ax * spd, 0.18);
    ship.vy = lerp(ship.vy, ay * spd, 0.18);

    ship.x += ship.vx * 60 * dt;
    ship.y += ship.vy * 60 * dt;

    // Границы
    ship.x = clamp(ship.x, ship.size, W - ship.size);
    ship.y = clamp(ship.y, ship.size + 40, H - ship.size);

    ship.enginePhase += dt * 12;

    // Частицы двигателя
    if (Math.hypot(ship.vx, ship.vy) > 0.5 || Math.random() < 0.3) {
        spawnParticle(
            ship.x + rand(-4, 4),
            ship.y + ship.size * 0.8,
            rand(-0.5, 0.5), rand(1.5, 3.5),
            rand(0.3, 0.6), rand(2, 4),
            Math.random() < 0.5 ? '#00e5ff' : '#0088ff'
        );
    }
}

function drawShip(time) {
    ctx.save();
    ctx.translate(ship.x, ship.y);

    // Неуязвимость — мигание
    if (invulnTimer > 0 && Math.sin(time * 18) > 0) {
        ctx.globalAlpha = 0.3;
    }

    let s = ship.size;

    // Свечение двигателя
    let eg = ctx.createRadialGradient(0, s * 0.5, 0, 0, s * 0.5, s * 0.9);
    eg.addColorStop(0, 'rgba(0,229,255,0.35)');
    eg.addColorStop(1, 'rgba(0,229,255,0)');
    ctx.fillStyle = eg;
    ctx.beginPath();
    ctx.arc(0, s * 0.5, s * 0.9, 0, Math.PI * 2);
    ctx.fill();

    // Пламя двигателя
    let flameLen = s * (0.5 + 0.3 * Math.sin(ship.enginePhase));
    ctx.beginPath();
    ctx.moveTo(-s * 0.25, s * 0.5);
    ctx.quadraticCurveTo(0, s * 0.5 + flameLen, s * 0.25, s * 0.5);
    ctx.fillStyle = 'rgba(0,200,255,0.7)';
    ctx.fill();
    ctx.beginPath();
    ctx.moveTo(-s * 0.12, s * 0.5);
    ctx.quadraticCurveTo(0, s * 0.5 + flameLen * 0.6, s * 0.12, s * 0.5);
    ctx.fillStyle = 'rgba(255,255,255,0.6)';
    ctx.fill();

    // Корпус
    ctx.beginPath();
    ctx.moveTo(0, -s);           // нос
    ctx.lineTo(-s * 0.35, -s * 0.2);
    ctx.lineTo(-s * 0.8, s * 0.5);  // левое крыло
    ctx.lineTo(-s * 0.3, s * 0.35);
    ctx.lineTo(0, s * 0.5);
    ctx.lineTo(s * 0.3, s * 0.35);
    ctx.lineTo(s * 0.8, s * 0.5);   // правое крыло
    ctx.lineTo(s * 0.35, -s * 0.2);
    ctx.closePath();

    let bg = ctx.createLinearGradient(0, -s, 0, s * 0.5);
    bg.addColorStop(0, '#b0e0ff');
    bg.addColorStop(0.5, '#4a9eff');
    bg.addColorStop(1, '#1a5ab8');
    ctx.fillStyle = bg;
    ctx.fill();
    ctx.strokeStyle = 'rgba(180,220,255,0.5)';
    ctx.lineWidth = 1.2;
    ctx.stroke();

    // Кабина
    ctx.beginPath();
    ctx.ellipse(0, -s * 0.3, s * 0.15, s * 0.28, 0, 0, Math.PI * 2);
    let cg = ctx.createRadialGradient(0, -s * 0.35, 0, 0, -s * 0.3, s * 0.25);
    cg.addColorStop(0, '#ffffff');
    cg.addColorStop(0.5, '#80d0ff');
    cg.addColorStop(1, '#0066aa');
    ctx.fillStyle = cg;
    ctx.fill();

    ctx.restore();
}

// ---- Кристаллы ----
function spawnCrystal() {
    let type = weightedRandom(CRYSTAL_TYPES);
    crystals.push({
        x: rand(type.sz + 10, W - type.sz - 10),
        y: -type.sz * 2,
        type: type,
        vy: rand(1.0, 2.0) * difficultyMul,
        vx: rand(-0.3, 0.3),
        rot: 0,
        rotSpeed: rand(-3, 3),
        pulse: rand(0, Math.PI * 2)
    });
}

function updateCrystals(dt) {
    for (let i = crystals.length - 1; i >= 0; i--) {
        let c = crystals[i];
        c.y += c.vy * 60 * dt;
        c.x += c.vx * 60 * dt;
        c.rot += c.rotSpeed * dt;
        c.pulse += dt * 4;

        // Столкновение с кораблём
        if (dist(c, ship) < ship.size * 0.7 + c.type.sz * 0.7) {
            collectCrystal(c);
            crystals.splice(i, 1);
            continue;
        }

        // Ушёл за экран
        if (c.y > H + 30) {
            crystals.splice(i, 1);
        }
    }
}

function drawCrystals(time) {
    for (let c of crystals) {
        ctx.save();
        ctx.translate(c.x, c.y);
        ctx.rotate(c.rot);

        let sz = c.type.sz;
        let pulseScale = 1 + 0.08 * Math.sin(c.pulse);
        ctx.scale(pulseScale, pulseScale);

        // Свечение
        let glow = ctx.createRadialGradient(0, 0, 0, 0, 0, sz * 2);
        glow.addColorStop(0, c.type.glow);
        glow.addColorStop(1, 'rgba(0,0,0,0)');
        ctx.fillStyle = glow;
        ctx.beginPath();
        ctx.arc(0, 0, sz * 2, 0, Math.PI * 2);
        ctx.fill();

        // Форма кристалла — восьмиугольник с вытянутыми гранями
        ctx.beginPath();
        let sides = 6;
        for (let j = 0; j < sides; j++) {
            let angle = (Math.PI * 2 / sides) * j - Math.PI / 2;
            let r = sz * (j % 2 === 0 ? 1 : 0.65);
            let px = Math.cos(angle) * r;
            let py = Math.sin(angle) * r;
            j === 0 ? ctx.moveTo(px, py) : ctx.lineTo(px, py);
        }
        ctx.closePath();

        let grad = ctx.createLinearGradient(-sz, -sz, sz, sz);
        grad.addColorStop(0, '#ffffff');
        grad.addColorStop(0.3, c.type.color);
        grad.addColorStop(1, c.type.color);
        ctx.fillStyle = grad;
        ctx.fill();
        ctx.strokeStyle = 'rgba(255,255,255,0.5)';
        ctx.lineWidth = 1;
        ctx.stroke();

        // Блик
        ctx.beginPath();
        ctx.arc(-sz * 0.2, -sz * 0.25, sz * 0.2, 0, Math.PI * 2);
        ctx.fillStyle = 'rgba(255,255,255,0.6)';
        ctx.fill();

        ctx.restore();
    }
}

function collectCrystal(c) {
    // Комбо
    if (comboTimer > 0) { combo++; } else { combo = 1; }
    comboTimer = 2.0;
    if (combo > maxCombo) maxCombo = combo;

    let multiplier = combo >= 5 ? 3 : combo >= 3 ? 2 : 1;
    let pts = c.type.pts * multiplier;
    score += pts;
    totalCollected++;

    // Всплывающий текст
    let text = '+' + pts;
    if (multiplier > 1) text += ' x' + multiplier;
    floatingTexts.push({
        x: c.x, y: c.y, text: text,
        color: c.type.color, life: 1.2, maxLife: 1.2
    });

    // Частицы сбора
    for (let j = 0; j < 14; j++) {
        let angle = rand(0, Math.PI * 2);
        let speed = rand(1.5, 5);
        spawnParticle(c.x, c.y,
            Math.cos(angle) * speed, Math.sin(angle) * speed,
            rand(0.3, 0.7), rand(2, 5), c.type.color);
    }

    updateHUD();
}

// ---- Астероиды ----
function createAsteroidShape(r, n) {
    let verts = [];
    for (let i = 0; i < n; i++) {
        let angle = (Math.PI * 2 / n) * i;
        let rr = r * rand(0.65, 1.2);
        verts.push({ x: Math.cos(angle) * rr, y: Math.sin(angle) * rr });
    }
    return verts;
}

function spawnAsteroid() {
    let r = rand(ASTEROID_MIN, ASTEROID_MAX);
    let numVerts = randInt(7, 12);
    asteroids.push({
        x: rand(r + 10, W - r - 10),
        y: -r * 2,
        r: r,
        vy: rand(1.2, 2.8) * difficultyMul,
        vx: rand(-0.5, 0.5),
        rot: 0,
        rotSpeed: rand(-2, 2),
        verts: createAsteroidShape(r, numVerts),
        shade: rand(0.3, 0.7) // оттенок
    });
}

function updateAsteroids(dt) {
    for (let i = asteroids.length - 1; i >= 0; i--) {
        let a = asteroids[i];
        a.y += a.vy * 60 * dt;
        a.x += a.vx * 60 * dt;
        a.rot += a.rotSpeed * dt;

        // Столкновение с кораблём
        if (invulnTimer <= 0 && dist(a, ship) < ship.size * 0.6 + a.r * 0.7) {
            hitByAsteroid(a);
            asteroids.splice(i, 1);
            continue;
        }

        // Ушёл за экран — засчитываем «уклонился»
        if (a.y > H + a.r + 20) {
            totalAsteroidsDodged++;
            asteroids.splice(i, 1);
        }
    }
}

function drawAsteroids() {
    for (let a of asteroids) {
        ctx.save();
        ctx.translate(a.x, a.y);
        ctx.rotate(a.rot);

        // Тень / свечение
        let sg = ctx.createRadialGradient(0, 0, a.r * 0.3, 0, 0, a.r * 1.5);
        sg.addColorStop(0, 'rgba(255,60,60,0.06)');
        sg.addColorStop(1, 'rgba(0,0,0,0)');
        ctx.fillStyle = sg;
        ctx.beginPath();
        ctx.arc(0, 0, a.r * 1.5, 0, Math.PI * 2);
        ctx.fill();

        // Форма
        ctx.beginPath();
        ctx.moveTo(a.verts[0].x, a.verts[0].y);
        for (let j = 1; j < a.verts.length; j++) {
            ctx.lineTo(a.verts[j].x, a.verts[j].y);
        }
        ctx.closePath();

        let sh = a.shade;
        let grad = ctx.createLinearGradient(-a.r, -a.r, a.r, a.r);
        grad.addColorStop(0, `rgba(${140*sh|0},${110*sh|0},${90*sh|0},1)`);
        grad.addColorStop(0.5, `rgba(${100*sh|0},${80*sh|0},${65*sh|0},1)`);
        grad.addColorStop(1, `rgba(${60*sh|0},${45*sh|0},${35*sh|0},1)`);
        ctx.fillStyle = grad;
        ctx.fill();
        ctx.strokeStyle = `rgba(${180*sh|0},${150*sh|0},${120*sh|0},0.5)`;
        ctx.lineWidth = 1.2;
        ctx.stroke();

        // Кратеры
        for (let j = 0; j < 3; j++) {
            let cx = a.verts[j * 3 % a.verts.length].x * 0.5;
            let cy = a.verts[j * 3 % a.verts.length].y * 0.5;
            let cr = a.r * rand(0.1, 0.18);
            ctx.beginPath();
            ctx.arc(cx, cy, Math.max(1, cr), 0, Math.PI * 2);
            ctx.fillStyle = `rgba(0,0,0,0.2)`;
            ctx.fill();
        }

        ctx.restore();
    }
}

function hitByAsteroid(a) {
    lives--;
    invulnTimer = INVULN_TIME;
    combo = 0;
    comboTimer = 0;
    shakeDur = 0.3;

    // Частицы взрыва
    for (let j = 0; j < 20; j++) {
        let angle = rand(0, Math.PI * 2);
        let speed = rand(2, 6);
        spawnParticle(a.x, a.y,
            Math.cos(angle) * speed, Math.sin(angle) * speed,
            rand(0.3, 0.8), rand(2, 6),
            Math.random() < 0.5 ? '#ff4444' : '#ff8844');
    }

    floatingTexts.push({
        x: ship.x, y: ship.y - 30, text: '-1 жизнь',
        color: '#ff3366', life: 1.5, maxLife: 1.5
    });

    updateHUD();

    if (lives <= 0) {
        endGame('lives');
    }
}

// ---- Частицы ----
function spawnParticle(x, y, vx, vy, life, size, color) {
    particles.push({ x, y, vx, vy, life, maxLife: life, size, color });
}

function updateParticles(dt) {
    for (let i = particles.length - 1; i >= 0; i--) {
        let p = particles[i];
        p.x += p.vx * 60 * dt;
        p.y += p.vy * 60 * dt;
        p.life -= dt;
        if (p.life <= 0) particles.splice(i, 1);
    }
}

function drawParticles() {
    for (let p of particles) {
        let alpha = clamp(p.life / p.maxLife, 0, 1);
        let sz = p.size * alpha;
        ctx.globalAlpha = alpha;
        ctx.fillStyle = p.color;
        ctx.beginPath();
        ctx.arc(p.x, p.y, Math.max(0.5, sz), 0, Math.PI * 2);
        ctx.fill();
    }
    ctx.globalAlpha = 1;
}

// ---- Всплывающие тексты ----
function updateFloatingTexts(dt) {
    for (let i = floatingTexts.length - 1; i >= 0; i--) {
        let ft = floatingTexts[i];
        ft.y -= 40 * dt;
        ft.life -= dt;
        if (ft.life <= 0) floatingTexts.splice(i, 1);
    }
}

function drawFloatingTexts() {
    for (let ft of floatingTexts) {
        let alpha = clamp(ft.life / ft.maxLife, 0, 1);
        let scale = 0.8 + 0.4 * (1 - alpha);
        ctx.save();
        ctx.translate(ft.x, ft.y);
        ctx.scale(scale, scale);
        ctx.globalAlpha = alpha;
        ctx.font = 'bold 18px "Segoe UI", sans-serif';
        ctx.textAlign = 'center';
        ctx.fillStyle = ft.color;
        ctx.shadowColor = ft.color;
        ctx.shadowBlur = 8;
        ctx.fillText(ft.text, 0, 0);
        ctx.shadowBlur = 0;
        ctx.restore();
    }
    ctx.globalAlpha = 1;
}

// ---- Тряска экрана ----
function updateShake(dt) {
    if (shakeDur > 0) {
        shakeDur -= dt;
        let intensity = shakeDur * 15;
        shakeX = rand(-intensity, intensity);
        shakeY = rand(-intensity, intensity);
    } else {
        shakeX = 0;
        shakeY = 0;
    }
}

// ---- Спавн объектов ----
function updateSpawning(dt) {
    spawnAccum += dt * 1000;
    let interval = Math.max(350, SPAWN_INTERVAL_BASE - (GAME_DURATION - timeLeft) * 8);

    while (spawnAccum >= interval) {
        spawnAccum -= interval;
        // Шанс: 60% кристалл, 40% астероид (растёт со временем)
        let elapsed = GAME_DURATION - timeLeft;
        let asteroidChance = 0.35 + elapsed * 0.005;
        if (Math.random() < asteroidChance) {
            spawnAsteroid();
        } else {
            spawnCrystal();
        }
    }
}

// ---- Управление ----
const keys = {};
window.addEventListener('keydown', e => {
    keys[e.code] = true;
    // Предотвращаем прокрутку стрелками
    if (['ArrowUp','ArrowDown','ArrowLeft','ArrowRight','Space'].includes(e.code)) {
        e.preventDefault();
    }
});
window.addEventListener('keyup', e => { keys[e.code] = false; });

// Тач-управление для мобильных
let touchStartX = 0, touchStartY = 0, touchActive = false;
canvas.addEventListener('touchstart', e => {
    e.preventDefault();
    let t = e.touches[0];
    touchStartX = t.clientX;
    touchStartY = t.clientY;
    touchActive = true;
}, { passive: false });
canvas.addEventListener('touchmove', e => {
    e.preventDefault();
    if (!touchActive || gameState !== 'playing') return;
    let t = e.touches[0];
    let dx = t.clientX - touchStartX;
    let dy = t.clientY - touchStartY;
    // Прямое перемещение корабля к пальцу
    ship.x = clamp(t.clientX, ship.size, W - ship.size);
    ship.y = clamp(t.clientY, ship.size + 40, H - ship.size);
    touchStartX = t.clientX;
    touchStartY = t.clientY;
}, { passive: false });
canvas.addEventListener('touchend', () => { touchActive = false; });

// ---- HUD ----
function updateHUD() {
    hudScore.textContent = score;
    hudTimer.textContent = Math.ceil(timeLeft);

    if (timeLeft <= 10) {
        hudTimer.classList.add('urgent');
    } else {
        hudTimer.classList.remove('urgent');
    }

    let heartsHTML = '';
    for (let i = 0; i < MAX_LIVES; i++) {
        heartsHTML += `<span class="heart${i >= lives ? ' lost' : ''}">&#10084;&#65039;</span>`;
    }
    hudHearts.innerHTML = heartsHTML;
}

// ---- Размеры канваса ----
function resize() {
    W = window.innerWidth;
    H = window.innerHeight;
    canvas.width = W;
    canvas.height = H;
    if (stars.length === 0) {
        initStars();
        initNebulae();
    }
}
window.addEventListener('resize', resize);
resize();

// ---- Инициализация игры ----
function initGame() {
    score = 0;
    lives = MAX_LIVES;
    timeLeft = GAME_DURATION;
    invulnTimer = 0;
    combo = 0;
    comboTimer = 0;
    maxCombo = 0;
    totalCollected = 0;
    totalAsteroidsDodged = 0;
    spawnAccum = 0;
    difficultyMul = 1;
    shakeX = 0; shakeY = 0; shakeDur = 0;

    ship = createShip();
    crystals = [];
    asteroids = [];
    particles = [];
    floatingTexts = [];

    updateHUD();
}

// ---- Запуск / завершение ----
function startGame() {
    initGame();
    gameState = 'playing';
    startScreen.classList.add('hidden');
    endScreen.classList.add('hidden');
    hud.classList.remove('hidden');
    lastTime = performance.now();
}

function endGame(reason) {
    gameState = 'ended';
    hud.classList.add('hidden');

    let isWin = reason === 'time' && lives > 0;

    endTitle.textContent = isWin ? 'Время вышло!' : 'Корабль уничтожен!';
    endTitle.className = 'end-title ' + (isWin ? 'win' : 'lose');

    endReason.textContent = isWin
        ? 'Ты продержался всю минуту — отличная работа!'
        : 'Астероиды оказались сильнее. Попробуй ещё раз!';

    // Оценка
    let rank = score >= 800 ? 'Легенда космоса' :
               score >= 500 ? 'Звёздный пилот' :
               score >= 250 ? 'Перспективный новичок' : 'Кадет';
    let rankColor = score >= 800 ? '#ffd700' :
                    score >= 500 ? '#00e5ff' :
                    score >= 250 ? '#00ff88' : '#ff55aa';

    statsGrid.innerHTML = `
        <div class="stat-card">
            <div class="stat-value gold">${score}</div>
            <div class="stat-label">Очки</div>
        </div>
        <div class="stat-card">
            <div class="stat-value">${totalCollected}</div>
            <div class="stat-label">Кристаллов собрано</div>
        </div>
        <div class="stat-card">
            <div class="stat-value">${maxCombo}x</div>
            <div class="stat-label">Макс. комбо</div>
        </div>
        <div class="stat-card">
            <div class="stat-value" style="color:${rankColor}">${rank}</div>
            <div class="stat-label">Звание</div>
        </div>
    `;

    // Задержка перед показом, чтобы видеть последние частицы
    setTimeout(() => {
        endScreen.classList.remove('hidden');
    }, 600);
}

// ---- Кнопки ----
btnStart.addEventListener('click', startGame);
btnRestart.addEventListener('click', startGame);

// Также по Enter/Space на стартовом экране
window.addEventListener('keydown', e => {
    if (e.code === 'Space' || e.code === 'Enter') {
        if (gameState === 'menu') startGame();
        else if (gameState === 'ended' && !endScreen.classList.contains('hidden')) startGame();
    }
});

// ---- Основной цикл ----
function gameLoop(now) {
    requestAnimationFrame(gameLoop);

    let dt = Math.min((now - lastTime) / 1000, 0.05);
    lastTime = now;

    // Обновление фона всегда
    updateStars(dt);
    updateNebulae(dt);

    // Очистка
    ctx.fillStyle = '#060b18';
    ctx.fillRect(0, 0, W, H);

    // Фон
    drawNebulae();
    drawStars(now / 1000);

    if (gameState === 'playing') {
        // Таймер
        timeLeft -= dt;
        if (timeLeft <= 0) {
            timeLeft = 0;
            endGame('time');
        }

        // Нарастающая сложность
        let elapsed = GAME_DURATION - timeLeft;
        difficultyMul = 1 + elapsed * 0.012;

        // Неуязвимость
        if (invulnTimer > 0) invulnTimer -= dt;

        // Комбо-таймер
        if (comboTimer > 0) {
            comboTimer -= dt;
            if (comboTimer <= 0) combo = 0;
        }

        updateShake(dt);
        updateSpawning(dt);
        updateShip(dt);
        updateCrystals(dt);
        updateAsteroids(dt);
        updateParticles(dt);
        updateFloatingTexts(dt);

        // Рисуем с тряской
        ctx.save();
        ctx.translate(shakeX, shakeY);

        drawCrystals(now / 1000);
        drawAsteroids();
        drawParticles();
        drawShip(now / 1000);
        drawFloatingTexts();

        ctx.restore();

        updateHUD();

        // Индикатор комбо
        if (combo >= 3 && comboTimer > 0) {
            let comboAlpha = clamp(comboTimer / 0.5, 0, 1);
            ctx.save();
            ctx.globalAlpha = comboAlpha * 0.9;
            ctx.font = 'bold 28px "Segoe UI", sans-serif';
            ctx.textAlign = 'center';
            ctx.fillStyle = combo >= 5 ? '#ffd700' : '#00e5ff';
            ctx.shadowColor = ctx.fillStyle;
            ctx.shadowBlur = 12;
            ctx.fillText(`COMBO x${combo}`, W / 2, H - 30);
            ctx.shadowBlur = 0;
            ctx.restore();
        }
    }

    // На экранах конца/меню всё равно рисуем частицы для красоты
    if (gameState === 'ended') {
        updateParticles(dt);
        drawParticles();
    }
}

// Запуск анимационного цикла (фон рисуется всегда)
lastTime = performance.now();
requestAnimationFrame(gameLoop);
</script>
</body>
</html>
