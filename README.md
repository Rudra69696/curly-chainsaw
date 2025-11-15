<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>KeyBoost Demo — Signup + Key Generator (24h)</title>
<style>
  :root{--bg:#061029;--panel:#0e1724;--accent:#2563eb;--muted:#9fb3d6}
  body{margin:0;font-family:Inter,Segoe UI,Arial;background:linear-gradient(180deg,var(--bg),#021025);color:#eaf4ff}
  .wrap{max-width:980px;margin:28px auto;padding:18px}
  header{display:flex;justify-content:space-between;align-items:center;gap:12px}
  .brand{font-weight:700;color:var(--accent)}
  .card{background:linear-gradient(180deg,rgba(255,255,255,0.02), rgba(255,255,255,0.01));border:1px solid rgba(255,255,255,0.03);padding:16px;border-radius:10px}
  .cols{display:flex;gap:16px;margin-top:16px}
  .col{flex:1}
  label{display:block;margin-top:8px;color:var(--muted);font-size:0.95rem}
  input,button,select,textarea{width:100%;padding:10px;margin-top:6px;border-radius:8px;border:0;background:rgba(255,255,255,0.02);color:#eaf4ff}
  .btn{background:var(--accent);color:#fff;border:0;cursor:pointer}
  .btn-ghost{background:transparent;border:1px solid rgba(255,255,255,0.04)}
  .tabs{display:flex;gap:8px;margin-top:12px}
  .tab-btn{padding:8px 12px;border-radius:8px;background:rgba(255,255,255,0.02);cursor:pointer}
  .tab-btn.active{background:var(--accent)}
  .key-box{font-family:ui-monospace,monospace;background:#071426;padding:12px;border-radius:8px;margin-top:10px}
  .key-item{background:rgba(255,255,255,0.02);padding:8px;border-radius:8px;margin-bottom:8px;display:flex;justify-content:space-between;align-items:center}
  .timer{color:#60a5fa;font-weight:600}
  .note{font-size:0.9rem;color:var(--muted);margin-top:8px}
  pre.viewer{background:#071426;padding:12px;border-radius:8px;white-space:pre-wrap;color:#dbeafe}
  footer{margin-top:18px;color:var(--muted);font-size:0.9rem}
  @media (max-width:900px){.cols{flex-direction:column}}
</style>
</head>
<body>
<div class="wrap">
  <header>
    <div class="brand">KeyBoost Demo</div>
    <div class="note">DEMO (client-only). Keys valid 24 hours.</div>
  </header>

  <div style="margin-top:14px" class="card">
    <div style="display:flex;gap:12px;flex-wrap:wrap;align-items:center">
      <div><strong id="who">Not logged in</strong></div>
      <div style="margin-left:auto">
        <button id="btnShowSignup" class="tab-btn">Signup</button>
        <button id="btnShowLogin" class="tab-btn">Login</button>
        <button id="btnLogout" class="tab-btn" style="display:none">Logout</button>
      </div>
    </div>

    <div class="tabs">
      <div class="tab-btn active" id="tabGenBtn">Generate</div>
      <div class="tab-btn" id="tabKeysBtn">Key (key.txt)</div>
      <div class="tab-btn" id="tabAccountBtn">Account</div>
    </div>

    <div id="area" style="margin-top:14px">
      <!-- GENERATE TAB -->
      <div id="tabGen">
        <div class="cols">
          <div class="col card">
            <h3>Generate Key</h3>
            <p class="note">Click to generate a numeric 4×4 key. It will be saved to your account and lives for 24 hours.</p>
            <button id="btnGen" class="btn">Generate Key</button>
            <div id="lastKey" class="key-box">No key yet</div>
            <div style="margin-top:10px">
              <button id="btnExportKey" class="btn-ghost">Download key.txt (for LuaDroid)</button>
            </div>
            <p class="note">To validate with LuaDroid: place <code>key.txt</code> in LuaDroid folder:
              <br><code>/storage/emulated/0/Android/data/com.lua_droid/files/users/&lt;username&gt;/key.txt</code>
            </p>
          </div>

          <div class="col card">
            <h3>Quick info</h3>
            <p class="note">Format in key.txt:</p>
            <pre class="viewer">KEY=XXXX-XXXX-XXXX-XXXX
TIME=UNIX_TIMESTAMP</pre>
            <p class="note">If multiple keys exist, the newest unexpired key is considered the "current" one for key.txt export.</p>
          </div>
        </div>
      </div>

      <!-- KEYS TAB -->
      <div id="tabKeys" style="display:none">
        <div class="card">
          <h3>Key (key.txt)</h3>
          <p class="note">Below are your active (unexpired) keys. Each has a 24-hour countdown. Expired keys are removed automatically.</p>

          <div id="keyList" style="margin-top:12px"></div>

          <h4 style="margin-top:12px">Virtual file: <code>key.txt</code></h4>
          <p class="note">This viewer shows the newest unexpired key (if any) in the exact format your Lua validator expects.</p>
          <pre id="keyTxtViewer" class="viewer">(no active key)</pre>
          <div style="margin-top:8px">
            <button id="btnDownload" class="btn-ghost">Download key.txt</button>
            <button id="btnCopy" class="tab-btn">Copy key.txt to clipboard</button>
          </div>
        </div>
      </div>

      <!-- ACCOUNT TAB -->
      <div id="tabAccount" style="display:none">
        <div class="cols">
          <div class="col card">
            <h3>Signup (Email, Username, Password)</h3>
            <label>Email</label>
            <input id="suEmail" placeholder="you@example.com">
            <label>Username</label>
            <input id="suUser" placeholder="username">
            <label>Password</label>
            <input id="suPass" type="password" placeholder="password">
            <button id="btnSignup" class="btn" style="margin-top:8px">Create account</button>
            <p id="suMsg" class="note"></p>
          </div>

          <div class="col card">
            <h3>Login</h3>
            <label>Username</label>
            <input id="liUser" placeholder="username">
            <label>Password</label>
            <input id="liPass" type="password" placeholder="password">
            <button id="btnLoginNow" class="btn" style="margin-top:8px">Login</button>
            <p id="liMsg" class="note"></p>
          </div>
        </div>
      </div>

    </div>
  </div>

  <footer>
    Demo — client-side only. Keys persist in this browser only. To use with LuaDroid, export key.txt and place it in the LuaDroid users folder.
  </footer>
</div>

<script>
/* -------------------------
   Client-side key system
   - Stores accounts and keys in localStorage
   - Each user: {email, username, password, keys: [{key, time}]}
   - Keys expire after 24 hours (86400 seconds)
   ------------------------- */

const LS_KEY = 'keyboost_demo_v1';

// load state
let state = JSON.parse(localStorage.getItem(LS_KEY) || '[]'); // array of users
let currentUser = null; // username string

function saveState(){ localStorage.setItem(LS_KEY, JSON.stringify(state)); }

// find user
function findUserByUsername(u){ return state.find(x => x.username === u); }

// UI refs
const who = document.getElementById('who');
const btnShowSignup = document.getElementById('btnShowSignup');
const btnShowLogin = document.getElementById('btnShowLogin');
const btnLogout = document.getElementById('btnLogout');

const tabGenBtn = document.getElementById('tabGenBtn');
const tabKeysBtn = document.getElementById('tabKeysBtn');
const tabAccountBtn = document.getElementById('tabAccountBtn');

const tabGen = document.getElementById('tabGen');
const tabKeys = document.getElementById('tabKeys');
const tabAccount = document.getElementById('tabAccount');

const btnGen = document.getElementById('btnGen');
const lastKey = document.getElementById('lastKey');
const keyList = document.getElementById('keyList');
const keyTxtViewer = document.getElementById('keyTxtViewer');
const btnDownload = document.getElementById('btnDownload');
const btnCopy = document.getElementById('btnCopy');
const btnExportKey = document.getElementById('btnExportKey');

const suEmail = document.getElementById('suEmail');
const suUser = document.getElementById('suUser');
const suPass = document.getElementById('suPass');
const btnSignup = document.getElementById('btnSignup');
const suMsg = document.getElementById('suMsg');

const liUser = document.getElementById('liUser');
const liPass = document.getElementById('liPass');
const btnLoginNow = document.getElementById('btnLoginNow');
const liMsg = document.getElementById('liMsg');

const btnShowLoginUI = document.getElementById('btnShowLogin');

// setup views
function showTab(name){
  tabGen.style.display = 'none';
  tabKeys.style.display = 'none';
  tabAccount.style.display = 'none';
  tabGenBtn.classList.remove('active');
  tabKeysBtn.classList.remove('active');
  tabAccountBtn.classList.remove('active');

  if(name==='gen'){ tabGen.style.display='block'; tabGenBtn.classList.add('active'); }
  if(name==='keys'){ tabKeys.style.display='block'; tabKeysBtn.classList.add('active'); renderKeys(); }
  if(name==='acct'){ tabAccount.style.display='block'; tabAccountBtn.classList.add('active'); }
}

tabGenBtn.addEventListener('click', ()=> showTab('gen'));
tabKeysBtn.addEventListener('click', ()=> showTab('keys'));
tabAccountBtn.addEventListener('click', ()=> showTab('acct'));

btnShowSignup.addEventListener('click', ()=> showTab('acct'));
btnShowLogin.addEventListener('click', ()=> showTab('acct'));

btnShowLoginUI.addEventListener('click', ()=> showTab('acct'));

// signup
btnSignup.addEventListener('click', ()=>{
  const email = suEmail.value.trim();
  const username = suUser.value.trim();
  const pass = suPass.value;

  if(!email || !username || !pass){ suMsg.innerText = 'Fill all fields'; return; }
  if(findUserByUsername(username)){ suMsg.innerText = 'Username already exists'; return; }

  state.push({ email: email, username: username, password: pass, keys: [] });
  saveState();
  suMsg.innerText = 'Account created. You can login below.';
  suEmail.value=''; suUser.value=''; suPass.value='';
});

// login
btnLoginNow.addEventListener('click', ()=>{
  const username = liUser.value.trim();
  const pass = liPass.value;

  const user = findUserByUsername(username);
  if(!user){ liMsg.innerText = 'User not found'; return; }
  if(user.password !== pass){ liMsg.innerText = 'Invalid password'; return; }

  // success
  currentUser = username;
  liUser.value=''; liPass.value='';
  liMsg.innerText = '';
  updateUIForLogin();
});

// logout
btnLogout.addEventListener('click', ()=>{
  currentUser = null;
  updateUIForLogout();
});

// generate key
function segment(){
  let s='';
  for(let i=0;i<4;i++) s += Math.floor(Math.random()*10);
  return s;
}
function makeKey(){ return `${segment()}-${segment()}-${segment()}-${segment()}`; }

btnGen.addEventListener('click', ()=>{
  if(!currentUser){ alert('Login first'); return; }
  const user = findUserByUsername(currentUser);
  // cleanup expired before generating
  cleanupExpiredKeys(user);

  const k = makeKey();
  const ts = Math.floor(Date.now()/1000);
  user.keys.push({ key: k, time: ts });
  saveState();
  lastKey.innerText = k;
  renderKeys();
});

// render keys for current user
function renderKeys(){
  keyList.innerHTML = '';
  keyTxtViewer.innerText = '(no active key)';
  if(!currentUser){ keyList.innerHTML = '<div class="note">Login to see keys</div>'; return; }
  const user = findUserByUsername(currentUser);
  if(!user) return;

  // remove expired and save
  cleanupExpiredKeys(user);
  saveState();

  if(user.keys.length === 0){
    keyList.innerHTML = '<div class="note">No active keys</div>';
    return;
  }

  // newest unexpired as first
  user.keys.slice().reverse().forEach(kobj => {
    const rem = (kobj.time + 86400) - Math.floor(Date.now()/1000);
    const h = Math.floor(rem/3600), m = Math.floor((rem%3600)/60), s = rem%60;
    const div = document.createElement('div');
    div.className = 'key-item';
    div.innerHTML = `<div>KEY=${kobj.key}</div><div class="timer">${h}h ${m}m ${s}s</div>`;
    keyList.appendChild(div);
  });

  // populate virtual key.txt with newest unexpired key
  const newest = user.keys[user.keys.length - 1];
  if(newest){
    keyTxtViewer.innerText = `KEY=${newest.key}\nTIME=${newest.time}`;
  }
}

// cleanup expired keys for a user
function cleanupExpiredKeys(user){
  if(!user) return;
  const now = Math.floor(Date.now()/1000);
  user.keys = user.keys.filter(k => now < (k.time + 86400));
}

// download key.txt (newest unexpired)
btnDownload.addEventListener('click', ()=>{
  if(!currentUser){ alert('Login first'); return; }
  const user = findUserByUsername(currentUser);
  cleanupExpiredKeys(user);
  if(user.keys.length === 0){ alert('No active key to export'); return; }
  const newest = user.keys[user.keys.length - 1];
  const content = `KEY=${newest.key}\nTIME=${newest.time}\n`;
  const blob = new Blob([content], {type:'text/plain'});
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'key.txt';
  a.click();
});

// copy virtual key.txt
btnCopy.addEventListener('click', ()=>{
  if(!currentUser){ alert('Login first'); return; }
  const user = findUserByUsername(currentUser);
  cleanupExpiredKeys(user);
  if(user.keys.length === 0){ alert('No active key to copy'); return; }
  const newest = user.keys[user.keys.length - 1];
  const content = `KEY=${newest.key}\nTIME=${newest.time}\n`;
  navigator.clipboard.writeText(content).then(()=> alert('Copied to clipboard'));
});

// export key.txt quick button (on Generate card)
btnExportKey.addEventListener('click', ()=>{
  // same as download button
  btnDownload.click();
});

// auto-update timers every second when Keys tab visible
setInterval(()=>{
  if(tabKeys.style.display === 'block') renderKeys();
}, 1000);

// UI helpers
function updateUIForLogin(){
  who.innerText = `${currentUser} (logged in)`;
  btnLogout.style.display = 'inline-block';
  btnShowSignup.style.display = 'none';
  btnShowLogin.style.display = 'none';
  showTab('gen');
  renderKeys();
}
function updateUIForLogout(){
  who.innerText = 'Not logged in';
  btnLogout.style.display = 'none';
  btnShowSignup.style.display = 'inline-block';
  btnShowLogin.style.display = 'inline-block';
  showTab('gen');
}

// auto-login if a single user exists? no. require manual login
// initial render
(function init(){
  // if no users, create a demo user for convenience
  if(state.length === 0){
    state.push({ email:'demo@local', username:'demo', password:'demo', keys: [] });
    saveState();
  }
  showTab('gen');
})();
</script>
</body>
</html>
