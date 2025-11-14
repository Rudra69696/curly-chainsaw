<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>24H Key Generator</title>
<style>
body { background:#0a1020; color:white; font-family:Arial; text-align:center; padding:40px; }
button {padding:10px 20px; margin:10px; border:none; border-radius:6px; background:#2563eb; color:white; cursor:pointer;}
.key {margin-top:20px; padding:15px; font-size:22px; background:#111827; border-radius:8px; display:inline-block; font-family:monospace;}
</style>
</head>
<body>

<h1>24-Hour Key Generator</h1>

<button onclick="generateKey()">Generate Key</button>
<button onclick="downloadKey()">Download key.txt</button>

<div id="output" class="key">Your key will appear here</div>

<script>
let currentKey = "";
let timestamp = 0;

// Make a 4-digit segment
function seg() {
    let s = "";
    for (let i = 0; i < 4; i++) s += Math.floor(Math.random() * 10);
    return s;
}

// Generate the key
function generateKey() {
    currentKey = seg()+"-"+seg()+"-"+seg()+"-"+seg();
    timestamp = Math.floor(Date.now() / 1000); // UNIX time
    document.getElementById("output").innerText = currentKey;
}

// Download key.txt
function downloadKey() {
    if (!currentKey) {
        alert("Generate a key first!");
        return;
    }

    let content = 
        "KEY=" + currentKey + "\n" +
        "TIME=" + timestamp + "\n";

    let blob = new Blob([content], {type:"text/plain"});
    let a = document.createElement("a");
    a.href = URL.createObjectURL(blob);
    a.download = "key.txt";
    a.click();
}
</script>

</body>
</html>
