<!DOCTYPE html>
<html lang="en"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Date Invitation — Activity Dashboard</title>
<link rel="preconnect" href="https://fonts.googleapis.com"><link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=Playfair+Display:wght@500;600;700&display=swap" rel="stylesheet">
<style>
*{box-sizing:border-box}body{margin:0;background:linear-gradient(135deg,#fff5f8,#f6eefb);font-family:"DM Sans",sans-serif;color:#442c3b}.wrap{max-width:900px;margin:35px auto;padding:0 18px}h1,h2{font-family:"Playfair Display",serif;color:#762e4c}h1{font-size:clamp(32px,5vw,48px)}.card{background:#ffffffd9;border:1px solid white;border-radius:22px;padding:22px;margin:18px 0;box-shadow:0 15px 45px #6c294510}.muted{color:#8c7582;line-height:1.7}.row{display:flex;gap:12px;flex-wrap:wrap}.btn{border:0;border-radius:99px;background:#d94f78;color:#fff;padding:12px 18px;font-weight:700;cursor:pointer}.btn.alt{background:#fff;color:#7a3b58;border:1px solid #efd3df}input{padding:12px;border:1px solid #e9cbd7;border-radius:10px;width:min(100%,420px);font:inherit}.event{padding:14px 0;border-bottom:1px solid #f0dce4}.event:last-child{border-bottom:0}.tag{display:inline-block;padding:4px 9px;border-radius:99px;background:#ffedf3;color:#a02e59;font-size:12px;font-weight:700}.time{font-size:12px;color:#9a8491}.empty{padding:20px;background:#fff7fa;border-radius:14px;color:#8c7582}a{color:#a02e59}
</style></head><body><main class="wrap">
<a href="index.html">← Back to the romantic website</a><h1>Little Love Notes — Activity Dashboard 💌</h1>
<p class="muted">When Firebase is configured, this page updates live with actions taken on the invitation website. It can show when the site is opened, which path she chooses, dates/times selected, destination choices, and when she opens the final notes.</p>
<div class="card"><h2>Connect your Firebase project</h2><p class="muted">Paste the same Firebase web-app configuration used in <code>index.html</code>. This is required for live updates.</p>
<div class="row"><input id="apiKey" placeholder="Firebase API key"><input id="projectId" placeholder="Firebase project ID"></div><div class="row" style="margin-top:10px"><input id="databaseURL" placeholder="Realtime Database URL"><input id="appId" placeholder="Firebase App ID"></div><div class="row" style="margin-top:10px"><button class="btn" id="connect">Connect &amp; watch live</button><button class="btn alt" id="clearLocal">Clear dashboard view</button></div><p class="muted" id="status">Not connected.</p></div>
<div class="card"><div class="row" style="justify-content:space-between;align-items:center"><h2 style="margin:0">Live activity</h2><span class="tag" id="liveTag">OFFLINE</span></div><p class="muted">Newest activity appears first. This dashboard only shows activity logged after Firebase is configured.</p><div id="events"><div class="empty">Connect your Firebase project to see activity.</div></div></div>
<div class="card"><h2>Privacy note</h2><p class="muted">This is a simple demo dashboard. Anyone who can access a public, unauthenticated database may be able to read or add activity records. The website does not collect her name, email, or precise location, but date/time and destination choices are still personal preferences. If you want private access, add Firebase Authentication and security rules before sharing the link.</p></div>
</main>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script><script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-database-compat.js"></script>
<script>
let db=null,listener=null;
const $=id=>document.getElementById(id);
function esc(s){return String(s??"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));}
$("connect").addEventListener("click",()=>{
 const config={apiKey:$("apiKey").value.trim(),authDomain:$("projectId").value.trim()+".firebaseapp.com",databaseURL:$("databaseURL").value.trim(),projectId:$("projectId").value.trim(),appId:$("appId").value.trim()};
 if(!config.apiKey||!config.databaseURL||!config.projectId||!config.appId){$("status").textContent="Please fill in all four fields.";return;}
 try{if(firebase.apps.length)firebase.apps.forEach(a=>a.delete());firebase.initializeApp(config);db=firebase.database();if(listener)listener.off();listener=db.ref("dateInvitationEvents").limitToLast(100);listener.on("value",snap=>{const list=[];snap.forEach(child=>list.push({key:child.key,...child.val()}));list.reverse();render(list);$("liveTag").textContent="LIVE";$("status").textContent="Connected. Waiting for new activity in real time.";},err=>{$("status").textContent="Connection error: "+err.message;});}
 catch(e){$("status").textContent="Could not connect: "+e.message;}
});
function render(items){const root=$("events");if(!items.length){root.innerHTML='<div class="empty">No activity recorded yet. Open the invitation in another tab/device and interact with it.</div>';return;}
 root.innerHTML=items.map(e=>{const detail=e.details||{};const label=(e.action||"activity").replaceAll("_"," ");const details=Object.entries(detail).map(([k,v])=>`<b>${esc(k)}:</b> ${esc(v)}`).join(" · ");return `<div class="event"><span class="tag">${esc(label)}</span><div style="margin-top:7px">${details||"Page activity"}</div><div class="time">${esc(e.at?new Date(e.at).toLocaleString():"Time unavailable")} · session ${esc((e.sessionId||"").slice(0,8))}</div></div>`}).join("");
}
$("clearLocal").addEventListener("click",()=>{$("events").innerHTML='<div class="empty">Dashboard view cleared. This does not delete database records.</div>';});
</script></body></html>
