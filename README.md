<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<title>24H Key Generator</title>
<style>
body {
    background: #0a1020;
    color: white;
    font-family: Arial;
    text-align: center;
    padding: 40px;
}
button {
    padding: 10px 20px;
    margin: 10px;
    border: none;
    border-radius: 6px;
    background: #2563eb;
    color: white;
    cursor: pointer;
}
.key-box {
    margin-top: 20px;
    padding: 15px;
    background: #111827;
    border-radius: 8px;
    font-size: 22px;
    font-family: monospace;
    display: inline-block;
}
.tab-btn {
    padding: 10px 20px;
    margin: 5px;
    border: none;
    border-radius: 6px;
    background: #1e293b;
    cursor: pointer;
    color: white;
}
.tab-btn.active {
    background: #2563eb;
}
.tab {
    display: none;
    margin-top: 30px;
}
.key-item {
    background: #111827;
    padding: 10px;
    margin: 10px auto;
    width: 360px;
    border-radius: 8px;
    font-family: monospace;
    text-align: left;
}
.timer {
    color: #38bdf8;
    font-size: 14px;
}
</style>
</head>
<body>

<h1>24-Hour Key Generator</h1>

<!-- TABS -->
<button class="tab-btn active" onclick="showTab('gen')">Generate</button>
<button class="tab-btn" onclick="showTab('keys')">Keys</button>

<!-- GENERATE TAB -->
<div id="gen" class="tab" style="display:block;">
    <button onclick="generateKey()">Generate Key</button>
    <div id="generated" class="key-box">Your key will appear here</div>
</div>

<!-- KEYS TAB -->
<div id="keys" class="tab">
    <h2>Saved Keys</h2>
    <div id="keyList"></div>
</div>

<script>
// =============================
// Storage
// =============================
let keys = JSON.parse(localStorage.getItem("keys") || "[]");

// =============================
// UI Tab Switch
// =============================
function showTab(name) {
    document.querySelectorAll(".tab").forEach(t => t.style.display = "none");
    document.querySelectorAll(".tab-btn").forEach(b => b.classList.remove("active"));
    document.getElementById(name).style.display = "block";
    event.target.classList.add("active");
    if (name === "keys") renderKeys();
}

// =============================
// Key Generator
// =============================
function segment() {
    let s = "";
    for (let i = 0; i < 4; i++) s += Math.floor(Math.random() * 10);
    return s;
}

function generateKey() {
    let key = segment() + "-" + segment() + "-" + segment() + "-" + segment();
    let created = Math.floor(Date.now() / 1000);

    keys.push({ key: key, time: created });
    localStorage.setItem("keys", JSON.stringify(keys));

    document.getElementById("generated").innerText = key;
}

// =============================
// Key Renderer + Timer
// =============================
function renderKeys() {
    let box = document.getElementById("keyList");
    box.innerHTML = "";

    let now = Math.floor(Date.now() / 1000);

    // Remove expired keys
    keys = keys.filter(k => now < k.time + 86400);
    localStorage.setItem("keys", JSON.stringify(keys));

    keys.forEach((k, index) => {
        let remaining = (k.time + 86400) - now;

        // Convert seconds → time left
        let h = Math.floor(remaining / 3600);
        let m = Math.floor((remaining % 3600) / 60);
        let s = remaining % 60;

        let el = document.createElement("div");
        el.className = "key-item";
        el.innerHTML = `
            KEY=${k.key}<br>
            <span class="timer">Expires in: ${h}h ${m}m ${s}s</span>
        `;

        box.appendChild(el);
    });
}

// Update timers every second
setInterval(() => {
    if (document.getElementById("keys").style.display === "block") {
        renderKeys();
    }
}, 1000);
</script>

</body>
</html>
