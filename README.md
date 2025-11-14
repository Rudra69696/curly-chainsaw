<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>C00LGUI (Web)</title>
  <style>
    :root{
      --bg: #0f1724;
      --panel: #111827;
      --fg: #E6EEF3;
      --muted: #9AA6B2;
      --accent: #7C3AED;
      --accent-2: #5B21B6;
      --width-sidebar: 220px;
      --ui-radius: 8px;
      font-family: "Segoe UI", system-ui, -apple-system, "Helvetica Neue", Arial;
    }
    .light{
      --bg: #F7FAFC;
      --panel: #FFFFFF;
      --fg: #0F1724;
      --muted: #4B5563;
      --accent: #2563EB;
      --accent-2: #1D4ED8;
    }

    html,body{
      height:100%;
      margin:0;
      background:var(--bg);
      color:var(--fg);
    }

    .app {
      display: grid;
      grid-template-columns: var(--width-sidebar) 1fr;
      height: 100vh;
      gap: 0;
      align-items: start;
      padding: 8px;
      box-sizing: border-box;
    }

    /* SIDEBAR */
    .sidebar{
      width: var(--width-sidebar);
      background: var(--panel);
      border-radius: 10px;
      padding: 18px;
      box-sizing: border-box;
      display:flex;
      flex-direction:column;
      gap:8px;
      align-items:stretch;
      min-height: calc(100vh - 16px);
    }
    .title {
      font-weight:700;
      font-size:18px;
      margin-bottom:6px;
    }
    .subtitle { font-size:12px; color:var(--muted); margin-bottom:12px; }

    .sidebar button {
      background:transparent;
      color:var(--fg);
      border:1px solid rgba(255,255,255,0.04);
      padding:10px 12px;
      border-radius:6px;
      cursor:pointer;
      text-align:left;
      font-weight:600;
    }
    .accent-btn{
      background: linear-gradient(90deg,var(--accent),var(--accent-2));
      color:white;
      border:none;
      box-shadow: 0 6px 18px rgba(0,0,0,0.15);
    }
    .sidebar .small { font-size:11px; color:var(--muted); margin-top:auto; }

    /* MAIN */
    .main {
      padding:12px;
      display:flex;
      flex-direction:column;
      gap:12px;
      box-sizing:border-box;
    }
    .header {
      height: 100px;
      position:relative;
      border-radius:10px;
      overflow:hidden;
      box-shadow: 0 6px 24px rgba(0,0,0,0.18);
      background: var(--panel);
    }
    /* fallback gradient if canvas not used */
    .header::before{
      content:"";
      position:absolute;
      inset:0;
      background: linear-gradient(90deg, rgba(0,0,0,0.02), rgba(0,0,0,0));
      z-index:0;
    }
    .canvas-layer{
      position:absolute;
      inset:0;
      z-index:0;
    }
    .header .header-text{
      position:relative;
      z-index:2;
      padding:12px;
      color:var(--fg);
      font-weight:700;
      font-size:16px;
    }

    .content {
      background: var(--panel);
      border-radius:10px;
      padding:14px;
      box-sizing:border-box;
      display:grid;
      grid-template-columns: 1fr;
      gap:12px;
    }

    .title-label { font-size:20px; font-weight:700; color:var(--fg); }
    .helper { color:var(--muted); font-size:13px; }

    .controls { display:flex; gap:8px; align-items:center; flex-wrap:wrap; }
    select, button, input { font-family:inherit; font-size:13px; }
    select { padding:8px 10px; border-radius:6px; border:1px solid rgba(255,255,255,0.04); background:transparent; color:var(--fg); }
    .action-btn { padding:8px 12px; border-radius:8px; border:none; cursor:pointer; }
    .mono-input{
      font-family: "Consolas", "Courier New", monospace;
      padding:10px;
      border-radius:6px;
      border:1px dashed rgba(255,255,255,0.06);
      background: transparent;
      color:var(--fg);
      width:100%;
      box-sizing:border-box;
    }

    .statusbar{
      grid-column: 1 / -1;
      background: transparent;
      color:var(--muted);
      padding:8px 4px;
      font-size:13px;
    }

    /* small screens */
    @media (max-width:820px){
      .app{ grid-template-columns: 1fr; }
      .sidebar{ width:auto; min-height: auto; order:2; }
      .main{ order:1; }
    }
  </style>
</head>
<body>
  <div id="root" class="app">
    <aside class="sidebar" role="navigation" aria-label="Sidebar">
      <div class="title">C00LGUI</div>
      <div class="subtitle">Secure tools & toys</div>

      <button id="btn-key" class="accent-btn">Generate API Key</button>
      <button id="btn-urlsafe">Randomize (URL-safe)</button>
      <button id="btn-group">Generate Keycode</button>
      <button id="btn-about">About</button>

      <button id="toggle-theme" style="margin-top:18px">Toggle Theme</button>

      <div class="small">Made with Web Crypto + HTML</div>
    </aside>

    <main class="main" role="main">
      <div class="header" aria-hidden="false">
        <canvas id="gradientCanvas" class="canvas-layer"></canvas>
        <canvas id="accentCanvas" class="canvas-layer"></canvas>
        <div class="header-text">C00LGUI</div>
      </div>

      <section class="content" aria-labelledby="section-title">
        <div>
          <div id="section-title" class="title-label">API Key Generator</div>
          <div class="helper" id="helper-text">Produce cryptographically secure API keys or grouped keycodes.</div>
        </div>

        <div class="controls" aria-hidden="false">
          <label for="typeSelect" style="font-weight:600">Type:</label>
          <select id="typeSelect" aria-label="key type">
            <option value="hex">hex</option>
            <option value="urlsafe">urlsafe</option>
          </select>

          <button id="generate52" class="action-btn accent-btn">Generate 52-char key</button>
          <button id="copyLast" class="action-btn" title="Copy last generated key">Copy last</button>
        </div>

        <div>
          <input id="keyField" class="mono-input" type="text" readonly value="(no key yet)" aria-label="generated key"/>
        </div>

        <div>
          <div id="helper2" class="helper">Click "Generate" or "Generate Keycode" to create keys.</div>
        </div>

        <div style="display:flex;gap:8px;">
          <button id="generate_grouped" class="action-btn">Generate Keycode</button>
          <input id="groups" type="number" min="1" max="8" value="3" style="width:64px;padding:8px;border-radius:6px;border:1px solid rgba(0,0,0,0.08)" title="Number of groups"/>
          <input id="groupSize" type="number" min="1" max="16" value="4" style="width:64px;padding:8px;border-radius:6px;border:1px solid rgba(0,0,0,0.08)" title="Group size"/>
        </div>

        <div class="statusbar" id="status">Ready</div>
      </section>

    </main>
  </div>

  <script>
    (function(){
      // Elements
      const btnKey = document.getElementById('btn-key');
      const btnUrl = document.getElementById('btn-urlsafe');
      const btnGroup = document.getElementById('btn-group');
      const btnAbout = document.getElementById('btn-about');
      const toggleThemeBtn = document.getElementById('toggle-theme');
      const generate52 = document.getElementById('generate52');
      const copyLast = document.getElementById('copyLast');
      const keyField = document.getElementById('keyField');
      const typeSelect = document.getElementById('typeSelect');
      const helperText = document.getElementById('helper-text');
      const status = document.getElementById('status');
      const generateGrouped = document.getElementById('generate_grouped');
      const groupsInput = document.getElementById('groups');
      const groupSizeInput = document.getElementById('groupSize');
      const root = document.documentElement;
      const app = document.getElementById('root');

      // Theme
      let dark = true;
      function applyTheme(){
        if(dark){
          document.body.classList.remove('light');
          document.body.classList.add('dark');
          app.classList.remove('light');
        } else {
          app.classList.add('light');
        }
        // redraw gradient because colors changed
        drawGradient();
      }
      toggleThemeBtn.addEventListener('click', () => {
        dark = !dark;
        applyTheme();
        setStatus('Theme toggled');
      });

      // STATUS helper
      function setStatus(text){
        status.textContent = text;
      }

      // Secure generators using Web Crypto
      function toHex(bytes){
        return Array.from(bytes).map(b => b.toString(16).padStart(2,'0')).join('');
      }

      function generateHex52(){
        // 52 hex chars = 26 bytes
        const arr = crypto.getRandomValues(new Uint8Array(26));
        return toHex(arr);
      }

      function generateUrlsafe52(){
        const charset = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_";
        // Need 52 chars. Use crypto to sample uniformly.
        const result = [];
        const needed = 52;
        const len = charset.length;
        // We'll generate random bytes and map into range using rejection sampling
        while(result.length < needed){
          const randoms = crypto.getRandomValues(new Uint8Array(64));
          for(let i=0;i<randoms.length && result.length<needed;i++){
            const r = randoms[i];
            // rejection sampling: accept if r < floor(256 / len) * len to be fair
            const threshold = Math.floor(256 / len) * len;
            if(r < threshold){
              result.push(charset[r % len]);
            }
          }
        }
        return result.join('');
      }

      function generateGroupedKey(groups=3, groupSize=4){
        const charset = "abcdefghijklmnopqrstuvwxyz0123456789";
        const total = groups * groupSize;
        // we can generate needed random numbers and map
        const res = [];
        const randoms = crypto.getRandomValues(new Uint8Array(total));
        for(let i=0;i<total;i++){
          res.push(charset[randoms[i] % charset.length]);
        }
        const parts = [];
        for(let g=0;g<groups;g++){
          parts.push(res.slice(g*groupSize, g*groupSize+groupSize).join(''));
        }
        return parts.join('-');
      }

      // Clipboard
      async function copyToClipboard(text){
        try{
          if(navigator.clipboard && navigator.clipboard.writeText){
            await navigator.clipboard.writeText(text);
            return true;
          } else {
            // fallback
            const ta = document.createElement('textarea');
            ta.value = text;
            ta.setAttribute('readonly','');
            ta.style.position = 'absolute';
            ta.style.left = '-9999px';
            document.body.appendChild(ta);
            ta.select();
            const ok = document.execCommand('copy');
            document.body.removeChild(ta);
            return ok;
          }
        } catch(e){
          return false;
        }
      }

      // Actions
      btnKey.addEventListener('click', () => {
        showKeyGenerator();
        const key = generateHex52();
        keyField.value = key;
        setStatus('Generated 52-char key');
      });

      function showKeyGenerator(){
        document.querySelector('.title-label').textContent = 'API Key Generator';
        helperText.textContent = 'Generate 52-character keys (hex or URL-safe).';
        keyField.value = '(no key yet)';
        setStatus('Ready');
      }

      generate52.addEventListener('click', () => {
        const t = typeSelect.value;
        const key = (t === 'hex') ? generateHex52() : generateUrlsafe52();
        keyField.value = key;
        setStatus('Generated 52-char key');
      });

      btnUrl.addEventListener('click', () => {
        typeSelect.value = 'urlsafe';
        generate52.click();
      });

      btnGroup.addEventListener('click', () => {
        generateGrouped.click();
      });

      generateGrouped.addEventListener('click', () => {
        const groups = Math.max(1, Math.min(8, parseInt(groupsInput.value || 3)));
        const groupSize = Math.max(1, Math.min(16, parseInt(groupSizeInput.value || 4)));
        const key = generateGroupedKey(groups, groupSize);
        keyField.value = key;
        setStatus('Generated grouped key');
      });

      copyLast.addEventListener('click', async () => {
        const text = keyField.value;
        if(!text || text === '(no key yet)'){
          alert('No key to copy. Generate one first.');
          return;
        }
        const ok = await copyToClipboard(text);
        if(ok){
          setStatus('Copied to clipboard');
        } else {
          alert('Clipboard copy failed on this platform.');
        }
      });

      btnAbout.addEventListener('click', () => {
        alert('C00LGUI — demo UI built with HTML + Web Crypto.\\n\\nFeatures:\\n• Generate secure 52-char keys\\n• Grouped random keycodes\\n• Theme toggle\\n• Copy to clipboard');
      });

      // DRAW gradient + animated circle in canvas
      const gradCanvas = document.getElementById('gradientCanvas');
      const accCanvas = document.getElementById('accentCanvas');
      let accX = 24, accDX = 1.2;
      let accRadius = 20;

      function resizeCanvases(){
        const header = document.querySelector('.header');
        const rect = header.getBoundingClientRect();
        for(const c of [gradCanvas, accCanvas]){
          c.width = Math.max(300, rect.width);
          c.height = Math.max(80, rect.height);
          c.style.width = rect.width + 'px';
          c.style.height = rect.height + 'px';
        }
        drawGradient();
      }

      function hexToRgb(hex){
        hex = hex.replace('#','');
        return [
          parseInt(hex.substring(0,2),16),
          parseInt(hex.substring(2,4),16),
          parseInt(hex.substring(4,6),16)
        ];
      }

      function drawGradient(){
        const ctx = gradCanvas.getContext('2d', { alpha: true });
        const w = gradCanvas.width, h = gradCanvas.height;
        ctx.clearRect(0,0,w,h);

        // pick colors from CSS variables
        const style = getComputedStyle(app);
        const panel = style.getPropertyValue('--panel').trim() || '#111827';
        const accent = style.getPropertyValue('--accent').trim() || '#7C3AED';

        const [r1,g1,b1] = hexToRgb(panel.replace(/\s/g,''));
        const [r2,g2,b2] = hexToRgb(accent.replace(/\s/g,''));

        const gradient = ctx.createLinearGradient(0,0,w,0);
        for(let i=0;i<=1;i+=0.02){
          const r = Math.round(r1 + (r2 - r1) * i);
          const g = Math.round(g1 + (g2 - g1) * i);
          const b = Math.round(b1 + (b2 - b1) * i);
          gradient.addColorStop(i, `rgb(${r},${g},${b})`);
        }
        ctx.fillStyle = gradient;
        ctx.fillRect(0,0,w,h);

        // title text
        ctx.font = "700 16px 'Segoe UI', Arial";
        ctx.fillStyle = getComputedStyle(app).getPropertyValue('--fg').trim() || '#E6EEF3';
        ctx.fillText('C00LGUI', 12, 26);
      }

      function animateAccent(){
        const ctx = accCanvas.getContext('2d', { alpha: true });
        const w = accCanvas.width, h = accCanvas.height;
        ctx.clearRect(0,0,w,h);
        // color
        const accent = getComputedStyle(app).getPropertyValue('--accent').trim() || '#7C3AED';
        ctx.beginPath();
        ctx.fillStyle = accent;
        ctx.shadowColor = accent;
        ctx.shadowBlur = 8;
        ctx.arc(accX, h/2, accRadius, 0, Math.PI*2);
        ctx.fill();
        accX += accDX;
        if(accX + accRadius > w - 24 || accX - accRadius < 24) accDX *= -1;
      }

      // loop
      function loop(){
        animateAccent();
        requestAnimationFrame(loop);
      }

      // initial resize + listeners
      window.addEventListener('resize', () => {
        resizeCanvases();
      });
      // set CSS variables from body to app root (for theme switching)
      function syncCssVars(){
        const bodyStyles = getComputedStyle(document.body);
        // if app has .light applied we use its own vars, otherwise root already has dark variables
        // keep simple: nothing to copy
      }

      // initialize
      resizeCanvases();
      applyTheme();
      requestAnimationFrame(loop);

      // keyboard: Enter on keyField to copy
      keyField.addEventListener('keydown', async (e) => {
        if(e.key === 'Enter'){
          e.preventDefault();
          const ok = await copyToClipboard(keyField.value);
          setStatus(ok ? 'Copied to clipboard' : 'Copy failed');
        }
      });

      // small helpers: clicking sidebar generate buttons
      btnKey.addEventListener('click', () => {
        typeSelect.value = 'hex';
      });

      // set accessible focus outlines for keyboard users
      document.addEventListener('keyup', (e) => {
        if(e.key === 'Tab') document.body.classList.add('show-focus');
      });

      // set initial status text
      setStatus('Ready');

    })();
  </script>
</body>
</html>
