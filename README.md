# Jarryd-s
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>Coopers Shoot — Labour Tracker</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { background: #111210; font-family: Georgia, serif; color: #ddd8cc; min-height: 100vh; padding-bottom: 70px; }
  .header { background: #1a1a17; border-bottom: 1px solid #2e2e28; padding: 20px 20px 16px; position: sticky; top: 0; z-index: 10; }
  .header-top { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 14px; }
  .header-label { font-size: 10px; letter-spacing: 4px; color: #6a6458; text-transform: uppercase; margin-bottom: 4px; }
  .header-total { font-size: 20px; font-weight: bold; color: #f0ebe0; }
  .header-total span { font-size: 13px; color: #6a6458; font-weight: normal; }
  .save-status { font-size: 10px; letter-spacing: 1px; text-transform: uppercase; display: flex; align-items: center; gap: 5px; margin-top: 4px; }
  .save-dot { width: 6px; height: 6px; border-radius: 50%; }
  .master-bar-wrap { position: relative; margin-bottom: 6px; }
  .master-pct { position: absolute; right: 0; top: -16px; font-size: 11px; font-weight: bold; }
  .bar-track { background: #2a2a25; border-radius: 6px; height: 10px; overflow: hidden; }
  .bar-fill { height: 100%; border-radius: 6px; transition: width 0.4s ease; }
  .remaining-label { font-size: 11px; color: #6a6458; }
  .alert { margin-top: 10px; padding: 9px 12px; border-radius: 0 6px 6px 0; font-size: 12px; }
  .alert-red { background: #2a1a1a; border-left: 3px solid #e05555; color: #e09090; }
  .alert-orange { background: #252018; border-left: 3px solid #e09955; color: #c8a060; }
  .task { border-bottom: 1px solid #1e1e1a; background: #111210; }
  .task.over { background: #1a1208; }
  .task-row { padding: 13px 20px; cursor: pointer; user-select: none; }
  .task-top { display: flex; justify-content: space-between; align-items: center; margin-bottom: 7px; }
  .task-name { font-size: 14px; font-weight: bold; color: #e8e0d0; }
  .task-name.over { color: #e09090; }
  .task-right { display: flex; align-items: center; gap: 8px; }
  .over-badge { font-size: 9px; letter-spacing: 1px; text-transform: uppercase; color: #e05555; border: 1px solid #e05555; padding: 2px 5px; border-radius: 3px; }
  .task-days { font-size: 12px; font-weight: bold; }
  .chevron { font-size: 10px; color: #3a3a35; transition: transform 0.2s; display: inline-block; }
  .chevron.open { transform: rotate(180deg); }
  .task-bar-track { background: #1e1e1a; border-radius: 4px; height: 5px; overflow: hidden; margin-bottom: 5px; }
  .task-bar-fill { height: 100%; border-radius: 4px; transition: width 0.3s ease; }
  .task-meta { display: flex; justify-content: space-between; font-size: 11px; color: #4a4845; }
  .task-desc-short { overflow: hidden; white-space: nowrap; text-overflow: ellipsis; max-width: 65%; }
  .expanded { padding: 4px 20px 18px; border-top: 1px solid #1e1e1a; display: none; }
  .expanded.open { display: block; }
  .expanded-desc { font-size: 11px; color: #6a6458; margin: 10px 0 14px; line-height: 1.7; }
  .stats-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 8px; margin-bottom: 16px; }
  .stat-box { background: #1a1a17; border-radius: 6px; padding: 10px 8px; text-align: center; }
  .stat-val { font-size: 16px; font-weight: bold; }
  .stat-label { font-size: 9px; color: #4a4845; text-transform: uppercase; letter-spacing: 1px; margin-top: 3px; }
  .section-label { font-size: 10px; color: #4a4845; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 8px; }
  .btn-row { display: flex; gap: 6px; flex-wrap: wrap; margin-bottom: 10px; }
  .btn { padding: 8px 14px; border-radius: 6px; font-family: Georgia, serif; font-size: 13px; cursor: pointer; border: 1px solid #3a3830; background: #252520; color: #d0c8b8; }
  .btn-add { background: #2a3a28; border-color: #3a5a35; color: #7aaa68; }
  .btn-remove { background: #1e1a1a; border-color: #3a2828; color: #a07070; font-size: 12px; padding: 7px 14px; }
  .btn-reset { background: #1e1a1a; border-color: #3a2828; color: #7a5050; font-size: 12px; padding: 7px 14px; }
  .custom-row { display: flex; gap: 4px; }
  .custom-input { width: 65px; padding: 8px 10px; background: #1e1e1a; border: 1px solid #3a3830; border-radius: 6px; color: #d0c8b8; font-size: 13px; font-family: Georgia, serif; }
  .remove-row { display: flex; gap: 8px; margin-bottom: 14px; }
  .note-area { width: 100%; padding: 8px 10px; background: #1a1a17; border: 1px solid #2e2e28; border-radius: 6px; color: #b0a890; font-size: 12px; font-family: Georgia, serif; resize: none; }
  .footer { position: fixed; bottom: 0; left: 0; right: 0; background: #1a1a17; border-top: 1px solid #2e2e28; padding: 12px 20px; display: flex; justify-content: space-between; align-items: center; }
  .footer-left { font-size: 11px; color: #6a6458; }
  .footer-right { font-size: 13px; font-weight: bold; }
</style>
</head>
<body>

<div class="header">
  <div class="header-top">
    <div>
      <div class="header-label">Coopers Shoot — Labour Tracker</div>
      <div class="header-total" id="totalDisplay">0.0 <span>of 201.5 days</span></div>
    </div>
    <div class="save-status">
      <div class="save-dot" id="saveDot" style="background:#5a7a50"></div>
      <span id="saveText" style="color:#5a7a50">Saved</span>
    </div>
  </div>
  <div class="master-bar-wrap">
    <div class="master-pct" id="masterPct" style="color:#6a9a5a">0%</div>
    <div class="bar-track"><div class="bar-fill" id="masterBar" style="width:0%;background:#6a9a5a"></div></div>
  </div>
  <div class="remaining-label" id="remainingLabel">201.5 days remaining</div>
  <div id="alertsZone"></div>
</div>

<div id="taskList"></div>

<div class="footer">
  <div class="footer-left" id="footerLeft">All tasks within budget</div>
  <div class="footer-right" id="footerRight" style="color:#6a9a5a">0.0 / 201.5d — 0%</div>
</div>

<script>
const TASKS = [
  { id:"demo", label:"Demolition", desc:"Demo pantry, veranda arches, roof, timber structure, deck screening, stairs, subfloor. Retain materials where possible. Adjustable posts to be reused.", allowed:16 },
  { id:"setout", label:"Set Out", desc:"Re-set out over poured footings for accurate post locations", allowed:3 },
  { id:"footings", label:"Footings", desc:"Excavate and pour footings", allowed:1.5 },
  { id:"subfloor", label:"Subfloor Construction", desc:"25 posts, bearers, joists for deck/floor, extra supports for glass balustrade, 23 sheets green tongue + 8 sheets OxyMag (50mm setdown)", allowed:25 },
  { id:"frame", label:"Frame Install", desc:"Install prefab frames, stand frames, bracing, tie-downs", allowed:9 },
  { id:"structural", label:"Structural Framing", desc:"Trusses, fascias, gables, prep for cladding before roofer installs battens", allowed:22 },
  { id:"roof", label:"Roof Structure", desc:"Veranda posts, beams, hips, wall plates, battens. Clad over prior to roof sheeting. Includes 1 day clean.", allowed:22 },
  { id:"structural_final", label:"Structural Works Final", desc:"Misc structural adjustments and finishing items", allowed:3 },
  { id:"wrap", label:"Wrap & Windows", desc:"Pro Clima wrap, install windows, tape and seal to make watertight", allowed:9 },
  { id:"cladding", label:"Cladding", desc:"Batten walls (18mm H3 vertical battens), vermin barrier, stops, install cladding", allowed:33 },
  { id:"eaves", label:"Eaves", desc:"Install FC eaves sheeting", allowed:3 },
  { id:"soundproofing", label:"Soundproofing", desc:"Install soundcheck to Katie's room", allowed:3 },
  { id:"linings", label:"Internal Linings", desc:"Install VJ MDF wall/ceiling linings", allowed:8.5 },
  { id:"internal_build", label:"Internal Build", desc:"General internal build items", allowed:1.5 },
  { id:"doors", label:"Doors", desc:"Install solid core doors (5), cavity sliders, build jambs, hang doors", allowed:5 },
  { id:"fitoff", label:"Internal Fit-Off", desc:"Architraves, skirtings, hardware, toilet roll holders, hooks etc.", allowed:10 },
  { id:"decks", label:"Decks", desc:"Construct decks", allowed:13 },
  { id:"underdeck", label:"Under Deck Screening", desc:"Install screening under deck", allowed:6 },
  { id:"external", label:"External Carpentry", desc:"Install hoods, reinstall arches, frame skylights", allowed:3 },
];

const TOTAL = 201.5;
const KEY = "coopers_labour_v1";
let used = {}, notes = {}, expanded = null, saveTimer = null;

function load() {
  try {
    const raw = localStorage.getItem(KEY);
    if (raw) { const d = JSON.parse(raw); used = d.used||{}; notes = d.notes||{}; }
  } catch(e) {}
  TASKS.forEach(t => { if(used[t.id]===undefined) used[t.id]=0; if(notes[t.id]===undefined) notes[t.id]=""; });
}

function save() {
  setSaveStatus("saving");
  clearTimeout(saveTimer);
  saveTimer = setTimeout(() => {
    try { localStorage.setItem(KEY, JSON.stringify({used,notes})); setSaveStatus("saved"); }
    catch(e) { setSaveStatus("error"); }
  }, 500);
}

function setSaveStatus(s) {
  const c = {saved:"#5a7a50",saving:"#8a8060",error:"#8a4040"};
  const l = {saved:"Saved",saving:"Saving…",error:"Error"};
  document.getElementById("saveDot").style.background = c[s];
  document.getElementById("saveText").style.color = c[s];
  document.getElementById("saveText").textContent = l[s];
}

function col(pct) {
  if(pct>=100) return "#e05555";
  if(pct>=80) return "#e09955";
  if(pct>=50) return "#d4b84a";
  return "#6a9a5a";
}

function r1(n) { return Math.round(n*10)/10; }

function totalUsed() { return r1(Object.values(used).reduce((a,v)=>a+(v||0),0)); }

function updateHeader() {
  const tu = totalUsed();
  const pct = Math.min(100, Math.round((tu/TOTAL)*100));
  const c = col(pct);
  document.getElementById("totalDisplay").innerHTML = `${tu.toFixed(1)} <span>of ${TOTAL} days</span>`;
  document.getElementById("masterPct").textContent = pct+"%";
  document.getElementById("masterPct").style.color = c;
  document.getElementById("masterBar").style.width = pct+"%";
  document.getElementById("masterBar").style.background = `linear-gradient(90deg,#6a9a5a,${c})`;
  document.getElementById("remainingLabel").textContent = `${r1(TOTAL-tu).toFixed(1)} days remaining`;
  document.getElementById("footerRight").textContent = `${tu.toFixed(1)} / ${TOTAL}d — ${pct}%`;
  document.getElementById("footerRight").style.color = c;
  const over = TASKS.filter(t=>(used[t.id]||0)>t.allowed);
  const near = TASKS.filter(t=>{ const u=used[t.id]||0; return u/t.allowed>=0.8&&u<=t.allowed; });
  let html="";
  if(over.length) html+=`<div class="alert alert-red">⚠ Over budget: ${over.map(t=>t.label).join(", ")}</div>`;
  if(near.length) html+=`<div class="alert alert-orange">⚡ Near limit: ${near.map(t=>t.label).join(", ")}</div>`;
  document.getElementById("alertsZone").innerHTML = html;
  if(over.length) document.getElementById("footerLeft").textContent=`${over.length} task${over.length>1?"s":""} over budget`;
  else if(near.length) document.getElementById("footerLeft").textContent=`${near.length} task${near.length!==1?"s":""} near limit`;
  else document.getElementById("footerLeft").textContent="All tasks within budget";
}

function updateTaskEl(task) {
  const u=used[task.id]||0, pct=Math.min(100,Math.round((u/task.allowed)*100)), over=u>task.allowed;
  const remaining=r1(Math.max(0,task.allowed-u)), c=col(pct);
  const el=document.getElementById("task_"+task.id); if(!el) return;
  el.className="task"+(over?" over":"");
  el.querySelector(".task-name").className="task-name"+(over?" over":"");
  el.querySelector(".task-days").textContent=`${u.toFixed(1)}/${task.allowed}d`;
  el.querySelector(".task-days").style.color=c;
  el.querySelector(".over-badge").style.display=over?"inline":"none";
  el.querySelector(".task-bar-fill").style.width=pct+"%";
  el.querySelector(".task-bar-fill").style.background=c;
  el.querySelector(".task-right-label").textContent=over?`${r1(u-task.allowed).toFixed(1)}d over`:`${remaining.toFixed(1)}d left`;
  el.querySelector(".task-right-label").style.color=over?"#e05555":"#5a5450";
  el.querySelector(".stat-used").textContent=u.toFixed(1)+"d";
  el.querySelector(".stat-used").style.color=c;
  el.querySelector(".stat-remain").textContent=(over?r1(u-task.allowed):remaining).toFixed(1)+"d";
  el.querySelector(".stat-remain").style.color=over?"#e05555":"#6a9a5a";
  el.querySelector(".stat-remain-label").textContent=over?"Over by":"Remaining";
}

function toggleExpand(id) {
  expanded = expanded===id ? null : id;
  TASKS.forEach(t => {
    const exp=document.getElementById("exp_"+t.id), chev=document.getElementById("chev_"+t.id);
    if(exp) exp.className="expanded"+(expanded===t.id?" open":"");
    if(chev) chev.className="chevron"+(expanded===t.id?" open":"");
  });
}

function addDays(id, val) {
  const num=parseFloat(val); if(isNaN(num)||num<=0) return;
  used[id]=r1((used[id]||0)+num);
  updateTaskEl(TASKS.find(t=>t.id===id)); updateHeader(); save();
  const inp=document.getElementById("custom_"+id); if(inp) inp.value="";
}

function subtractHalf(id) {
  used[id]=r1(Math.max(0,(used[id]||0)-0.5));
  updateTaskEl(TASKS.find(t=>t.id===id)); updateHeader(); save();
}

function resetTask(id) {
  used[id]=0;
  updateTaskEl(TASKS.find(t=>t.id===id)); updateHeader(); save();
}

function updateNote(id, val) { notes[id]=val; save(); }

function buildUI() {
  const list=document.getElementById("taskList"); list.innerHTML="";
  TASKS.forEach(task => {
    const u=used[task.id]||0, pct=Math.min(100,Math.round((u/task.allowed)*100));
    const over=u>task.allowed, remaining=r1(Math.max(0,task.allowed-u)), c=col(pct);
    const descShort=task.desc.length>48?task.desc.slice(0,48)+"…":task.desc;
    const div=document.createElement("div");
    div.id="task_"+task.id;
    div.className="task"+(over?" over":"");
    div.innerHTML=`
      <div class="task-row" onclick="toggleExpand('${task.id}')">
        <div class="task-top">
          <div class="task-name${over?" over":""}">${task.label}</div>
          <div class="task-right">
            <span class="over-badge" style="display:${over?"inline":"none"}">Over</span>
            <span class="task-days" style="color:${c}">${u.toFixed(1)}/${task.allowed}d</span>
            <span class="chevron" id="chev_${task.id}">▼</span>
          </div>
        </div>
        <div class="task-bar-track"><div class="task-bar-fill" style="width:${pct}%;background:${c}"></div></div>
        <div class="task-meta">
          <span class="task-desc-short">${descShort}</span>
          <span class="task-right-label" style="color:${over?"#e05555":"#5a5450"};flex-shrink:0;margin-left:8px">${over?r1(u-task.allowed).toFixed(1)+"d over":remaining.toFixed(1)+"d left"}</span>
        </div>
      </div>
      <div class="expanded" id="exp_${task.id}">
        <div class="expanded-desc">${task.desc}</div>
        <div class="stats-grid">
          <div class="stat-box"><div class="stat-val" style="color:#6a6458">${task.allowed}d</div><div class="stat-label">Allowed</div></div>
          <div class="stat-box"><div class="stat-val stat-used" style="color:${c}">${u.toFixed(1)}d</div><div class="stat-label">Used</div></div>
          <div class="stat-box"><div class="stat-val stat-remain" style="color:${over?"#e05555":"#6a9a5a"}">${(over?r1(u-task.allowed):remaining).toFixed(1)}d</div><div class="stat-label stat-remain-label">${over?"Over by":"Remaining"}</div></div>
        </div>
        <div class="section-label">Add days</div>
        <div class="btn-row">
          ${[0.5,1,1.5,2,3].map(n=>`<button class="btn" onclick="addDays('${task.id}',${n})">+${n}d</button>`).join("")}
          <div class="custom-row">
            <input class="custom-input" id="custom_${task.id}" type="number" min="0" step="0.5" placeholder="other">
            <button class="btn btn-add" onclick="addDays('${task.id}',document.getElementById('custom_${task.id}').value)">Add</button>
          </div>
        </div>
        <div class="remove-row">
          <button class="btn btn-remove" onclick="subtractHalf('${task.id}')">− 0.5d</button>
          <button class="btn btn-reset" onclick="resetTask('${task.id}')">Reset to 0</button>
        </div>
        <div class="section-label">Note</div>
        <textarea class="note-area" rows="2" placeholder="Add a site note…" oninput="updateNote('${task.id}',this.value)">${notes[task.id]||""}</textarea>
      </div>`;
    list.appendChild(div);
  });
}

load(); buildUI(); updateHeader();
</script>
</body>
</html>
