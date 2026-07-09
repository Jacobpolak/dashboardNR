<!DOCTYPE html>
<html lang="nl">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Team Dashboard – Wie doet nu wat</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  :root{--primary:#1F4E78;--primary-light:#2E6DA4;--bg:#F4F6F9;--card:#FFFFFF;--text:#1F2937;--muted:#6B7280;--success:#10B981;--warning:#F59E0B;--danger:#EF4444;--info:#3B82F6;--border:#E5E7EB;}
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'Segoe UI',Tahoma,sans-serif;background:var(--bg);color:var(--text);line-height:1.5;padding:24px;min-height:100vh}
  .container{max-width:1400px;margin:0 auto}
  header{background:linear-gradient(135deg,var(--primary),var(--primary-light));color:#fff;padding:28px 32px;border-radius:14px;margin-bottom:24px;box-shadow:0 4px 12px rgba(0,0,0,.08)}
  header h1{font-size:28px;margin-bottom:6px}
  header p{opacity:.9;font-size:15px}
  .kpis{display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:16px;margin-bottom:24px}
  .kpi{background:var(--card);padding:20px;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,.05);border-left:4px solid var(--primary)}
  .kpi .label{color:var(--muted);font-size:13px;text-transform:uppercase;letter-spacing:.5px;font-weight:600}
  .kpi .value{font-size:32px;font-weight:700;color:var(--primary);margin-top:6px}
  .kpi.warn{border-left-color:var(--warning)} .kpi.warn .value{color:var(--warning)}
  .kpi.success{border-left-color:var(--success)} .kpi.success .value{color:var(--success)}
  .kpi.info{border-left-color:var(--info)} .kpi.info .value{color:var(--info)}
  .chart-filter-bar{background:var(--card);border-radius:12px;padding:14px 20px;margin-bottom:16px;box-shadow:0 2px 6px rgba(0,0,0,.05);display:flex;align-items:center;gap:12px;flex-wrap:wrap}
  .chart-filter-bar label{font-weight:600;color:var(--primary);font-size:14px}
  .chart-filter-bar select{padding:9px 12px;border:1px solid var(--border);border-radius:8px;font-size:14px;background:#fff;font-family:inherit;min-width:220px}
  .chart-filter-bar select:focus{outline:none;border-color:var(--primary);box-shadow:0 0 0 3px rgba(31,78,120,.15)}
  .chart-filter-bar .hint{color:var(--muted);font-size:13px;margin-left:auto}
  .chart-filter-bar .btn-reset{padding:6px 12px;font-size:12px;background:var(--bg);color:var(--primary);border:1px solid var(--border);border-radius:6px;cursor:pointer;font-family:inherit;font-weight:600}
  .chart-filter-bar .btn-reset:hover{background:#E5E7EB}
  .charts{display:grid;grid-template-columns:1fr 1fr;gap:20px;margin-bottom:24px}
  @media (max-width:900px){.charts{grid-template-columns:1fr}}
  .card{background:var(--card);border-radius:12px;padding:20px;box-shadow:0 2px 6px rgba(0,0,0,.05)}
  .card h2{font-size:17px;color:var(--primary);margin-bottom:6px}
  .card .subtitle{color:var(--muted);font-size:12px;margin-bottom:12px;padding-bottom:10px;border-bottom:2px solid var(--bg)}
  .chart-wrap{position:relative;height:280px}
  .toolbar{display:flex;flex-wrap:wrap;gap:12px;margin-bottom:20px;align-items:center}
  .toolbar input,.toolbar select{padding:9px 12px;border:1px solid var(--border);border-radius:8px;font-size:14px;background:#fff;font-family:inherit}
  .toolbar input:focus,.toolbar select:focus{outline:none;border-color:var(--primary);box-shadow:0 0 0 3px rgba(31,78,120,.15)}
  .toolbar .search{flex:1;min-width:200px}
  .btn{padding:10px 18px;border:none;border-radius:8px;font-size:14px;font-weight:600;cursor:pointer;transition:all .15s;font-family:inherit;display:inline-flex;align-items:center;gap:6px}
  .btn-primary{background:var(--primary);color:#fff} .btn-primary:hover{background:var(--primary-light)}
  .btn-secondary{background:#fff;color:var(--primary);border:1px solid var(--border)} .btn-secondary:hover{background:var(--bg)}
  .btn-danger{background:var(--danger);color:#fff} .btn-danger:hover{opacity:.9}
  .btn-sm{padding:6px 10px;font-size:12px}
  .highlight-section{background:#FEF3C7;border-left:4px solid var(--warning);border-radius:12px;padding:18px 20px;margin-bottom:24px}
  .highlight-section h2{color:#92400E;font-size:16px;margin-bottom:10px}
  .highlight-list{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:10px}
  .highlight-item{background:#fff;padding:12px;border-radius:8px;font-size:13px}
  .highlight-item strong{color:var(--primary);display:block;margin-bottom:2px}
  table{width:100%;border-collapse:collapse;font-size:14px}
  th{background:var(--primary);color:#fff;padding:12px 10px;text-align:left;font-weight:600;font-size:13px;text-transform:uppercase;letter-spacing:.3px}
  td{padding:12px 10px;border-bottom:1px solid var(--border)}
  tr:hover td{background:var(--bg)}
  tr:last-child td{border-bottom:none}
  .modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,.5);display:none;align-items:center;justify-content:center;z-index:100;padding:20px}
  .modal-overlay.active{display:flex}
  .modal{background:#fff;border-radius:14px;padding:28px;max-width:520px;width:100%;max-height:90vh;overflow-y:auto;box-shadow:0 20px 50px rgba(0,0,0,.2)}
  .modal h2{color:var(--primary);margin-bottom:20px;font-size:20px}
  .form-group{margin-bottom:16px}
  .form-group label{display:block;font-weight:600;color:var(--text);margin-bottom:6px;font-size:13px}
  .form-group input,.form-group select,.form-group textarea{width:100%;padding:10px 12px;border:1px solid var(--border);border-radius:8px;font-size:14px;font-family:inherit;background:#fff}
  .form-group textarea{resize:vertical;min-height:70px}
  .modal-actions{display:flex;gap:10px;justify-content:flex-end;margin-top:24px}
  .empty{text-align:center;padding:40px;color:var(--muted)}
  .actions-cell{display:flex;gap:6px}
  .footer{text-align:center;color:var(--muted);font-size:12px;margin-top:24px;padding:16px}
</style>
</head>
<body>
<div class="container">
  <header>
    <h1>👥 Team Dashboard – Wie doet nu wat</h1>
    <p>NuRijnland Perceelplanning · Realtime overzicht van toewijzingen per teamlid en project</p>
  </header>
  <div class="kpis">
    <div class="kpi"><div class="label">Teamleden</div><div class="value" id="kpi-teamleden">0</div></div>
    <div class="kpi success"><div class="label">Actieve toewijzingen</div><div class="value" id="kpi-actief">0</div></div>
    <div class="kpi info"><div class="label">Projecten in Bouwteamfase</div><div class="value" id="kpi-bouwteam">0</div></div>
    <div class="kpi success"><div class="label">Projecten in Realisatiefase</div><div class="value" id="kpi-realisatie">0</div></div>
    <div class="kpi warn"><div class="label">Aflopend deze week</div><div class="value" id="kpi-week">0</div></div>
  </div>

  <div class="chart-filter-bar">
    <label for="chartTeamlidFilter">🔍 Filter grafieken op teamlid:</label>
    <select id="chartTeamlidFilter">
      <option value="">Alle teamleden</option>
    </select>
    <button class="btn-reset" onclick="resetChartFilter()">Reset</button>
    <span class="hint" id="chartFilterHint">Toont data van alle teamleden</span>
  </div>

  <div class="charts">
    <div class="card">
      <h2>Verdeling per fase</h2>
      <div class="subtitle" id="chartFaseSubtitle">Alle teamleden</div>
      <div class="chart-wrap"><canvas id="chartFase"></canvas></div>
    </div>
    <div class="card">
      <h2>Toewijzingen per teamlid</h2>
      <div class="subtitle" id="chartTeamlidSubtitle">Alle teamleden</div>
      <div class="chart-wrap"><canvas id="chartTeamlid"></canvas></div>
    </div>
  </div>

  <div id="highlightSection" class="highlight-section" style="display:none">
    <h2>⚠️ Deze week aflopend (binnen 7 dagen)</h2>
    <div class="highlight-list" id="highlightList"></div>
  </div>

  <div class="card">
    <h2>Toewijzingen</h2>
    <div class="toolbar">
      <input type="text" class="search" id="searchInput" placeholder="🔍 Zoeken op naam, project, opmerking..." />
      <select id="filterTeamlid"><option value="">Alle teamleden</option></select>
      <select id="filterProject"><option value="">Alle projecten</option></select>
      <select id="filterFase">
        <option value="">Alle fasen</option>
        <option value="VO-fase">VO-fase</option>
        <option value="DO-fase">DO-fase</option>
        <option value="UO-fase">UO-fase</option>
        <option value="Realisatie">Realisatie</option>
      </select>
      <button class="btn btn-primary" onclick="openModal()">+ Nieuwe toewijzing</button>
      <button class="btn btn-secondary" onclick="exportData()">⬇ Export JSON</button>
      <button class="btn btn-secondary" onclick="document.getElementById('importInput').click()">⬆ Import JSON</button>
      <input type="file" id="importInput" accept=".json" style="display:none" onchange="importData(event)" />
    </div>
    <div style="overflow-x:auto">
      <table id="dataTable">
        <thead><tr>
          <th>Teamlid</th><th>Project</th><th>Huidige fase</th>
          <th>Einddatum fase</th><th>Start Realisatie</th>
          <th>Opmerkingen</th><th>Acties</th>
        </tr></thead>
        <tbody id="tableBody"></tbody>
      </table>
    </div>
    <div id="emptyState" class="empty" style="display:none">Geen toewijzingen gevonden. Klik op "+ Nieuwe toewijzing" om te beginnen.</div>
  </div>
  <div class="footer">💾 Gegevens worden lokaal in je browser opgeslagen (localStorage). Gebruik Export/Import om te delen of te backuppen.</div>
</div>

<div class="modal-overlay" id="modal">
  <div class="modal">
    <h2 id="modalTitle">Nieuwe toewijzing</h2>
    <form id="entryForm" onsubmit="saveEntry(event)">
      <input type="hidden" id="entryId" />
      <div class="form-group"><label>Teamlid *</label><input type="text" id="teamlid" required placeholder="Bijv. Wouter Lindenbergh"/></div>
      <div class="form-group"><label>Project *</label><select id="project" required></select></div>
      <div class="form-group"><label>Huidige fase *</label>
        <select id="fase" required>
          <option value="">-- Kies fase --</option>
          <option value="VO-fase">VO-fase</option>
          <option value="DO-fase">DO-fase</option>
          <option value="UO-fase">UO-fase</option>
          <option value="Realisatie">Realisatie</option>
        </select>
      </div>
      <div class="form-group"><label>Einddatum huidige fase</label><input type="date" id="einddatum"/></div>
      <div class="form-group"><label>Startdatum Realisatie</label><input type="date" id="startRealisatie"/></div>
      <div class="form-group"><label>Opmerkingen</label><textarea id="opmerkingen" placeholder="Optionele notities..."></textarea></div>
      <div class="modal-actions">
        <button type="button" class="btn btn-secondary" onclick="closeModal()">Annuleren</button>
        <button type="submit" class="btn btn-primary">Opslaan</button>
      </div>
    </form>
  </div>
</div>

<script>
const PROJECTS = ["Middenweg Ouderkerk a.d. Amstel","Rozenburg-Zuid fase 1: Rijsenhout Zuid","Versnelling RZF1a","Rozenburg-Zuid fase 2: Schiphol logistics park","Versnelling RZF2a","Rozenburg-Zuid fase 3: Beukenhorst oost/de Hoek","Rozenburg-Zuid fase 4","Rijsenhout-Noord","Oosteinde","Gildenlaan Velsen-noord","Wijkerstraatweg Velsen-noord","Wijkermeerweg Velsen-noord","Bloemendaalseweg Overveen","Rollandslaan Overveen","Hartelustlaan Overveen","Den Texlaan Overveen","Planetenlaan Haarlem","Muiderslotweg Haarlem","Kleverlaan Haarlem","Kennemerboulevard IJmuiden","Zadelmakerstraat"];
const FASES = ["VO-fase","DO-fase","UO-fase","Realisatie"];
const BOUWTEAM_FASES = ["VO-fase","DO-fase","UO-fase"];
const STORAGE_KEY = "teamDashboard_v1";
let entries = [], chartFase, chartTeamlid;

function init(){
  const proj = document.getElementById('project'), filterProj = document.getElementById('filterProject');
  PROJECTS.forEach(p=>{proj.innerHTML += `<option value="${p}">${p}</option>`; filterProj.innerHTML += `<option value="${p}">${p}</option>`;});
  loadData();
  if(entries.length===0) seedExample();
  render();
  ['searchInput','filterTeamlid','filterProject','filterFase'].forEach(id=>document.getElementById(id).addEventListener('input', render));
  document.getElementById('chartTeamlidFilter').addEventListener('change', updateCharts);
}
function todayPlus(days){const d=new Date();d.setDate(d.getDate()+days);return d.toISOString().slice(0,10);}
function seedExample(){
  entries = [
    {id:crypto.randomUUID(), teamlid:"Wouter Lindenbergh", project:"Middenweg Ouderkerk a.d. Amstel", fase:"UO-fase", einddatum:todayPlus(3), startRealisatie:todayPlus(40), opmerkingen:"Loopt op schema"},
    {id:crypto.randomUUID(), teamlid:"Wouter Lindenbergh", project:"Oosteinde", fase:"DO-fase", einddatum:todayPlus(60), startRealisatie:todayPlus(365), opmerkingen:""},
    {id:crypto.randomUUID(), teamlid:"Sander Zuyderwijk", project:"Rozenburg-Zuid fase 1: Rijsenhout Zuid", fase:"DO-fase", einddatum:todayPlus(20), startRealisatie:todayPlus(160), opmerkingen:""},
    {id:crypto.randomUUID(), teamlid:"Max Wiercx", project:"Planetenlaan Haarlem", fase:"Realisatie", einddatum:todayPlus(200), startRealisatie:todayPlus(-30), opmerkingen:"Uitvoering gestart"},
    {id:crypto.randomUUID(), teamlid:"Allard-Mark Ebbers", project:"Gildenlaan Velsen-noord", fase:"UO-fase", einddatum:todayPlus(5), startRealisatie:todayPlus(28), opmerkingen:"Klantoverleg gepland"}
  ];
  saveData();
}
function loadData(){const raw=localStorage.getItem(STORAGE_KEY);entries=raw?JSON.parse(raw):[];}
function saveData(){localStorage.setItem(STORAGE_KEY, JSON.stringify(entries));}
function getStatus(e){
  const today=new Date().toISOString().slice(0,10);
  if(e.startRealisatie && e.startRealisatie<=today && e.fase==="Realisatie") return {label:"Actief in realisatie",cls:"badge-active"};
  if(e.einddatum && e.einddatum<today) return {label:"Fase afgerond",cls:"badge-done"};
  if(e.einddatum){const diff=(new Date(e.einddatum)-new Date(today))/(1000*60*60*24);if(diff<=7) return {label:"Loopt af",cls:"badge-warn"};}
  if(e.startRealisatie && e.startRealisatie>today) return {label:"Aankomend",cls:"badge-upcoming"};
  return {label:"Actief",cls:"badge-active"};
}
function fmtDate(s){if(!s) return "—";const[y,m,d]=s.split("-");return `${d}-${m}-${y}`;}
function render(){
  refreshTeamlidFilter();
  refreshChartTeamlidFilter();
  const search=document.getElementById('searchInput').value.toLowerCase();
  const fTeam=document.getElementById('filterTeamlid').value;
  const fProj=document.getElementById('filterProject').value;
  const fFase=document.getElementById('filterFase').value;
  const filtered=entries.filter(e=>{
    if(fTeam && e.teamlid!==fTeam) return false;
    if(fProj && e.project!==fProj) return false;
    if(fFase && e.fase!==fFase) return false;
    if(search){const hay=`${e.teamlid} ${e.project} ${e.fase} ${e.opmerkingen||""}`.toLowerCase();if(!hay.includes(search)) return false;}
    return true;
  });
  const body=document.getElementById('tableBody'), empty=document.getElementById('emptyState');
  if(filtered.length===0){body.innerHTML="";empty.style.display="block";}
  else{
    empty.style.display="none";
    body.innerHTML=filtered.map(e=>{
      return `<tr><td><strong>${escapeHtml(e.teamlid)}</strong></td><td>${escapeHtml(e.project)}</td><td>${escapeHtml(e.fase)}</td><td>${fmtDate(e.einddatum)}</td><td>${fmtDate(e.startRealisatie)}</td><td>${escapeHtml(e.opmerkingen||"")}</td><td class="actions-cell"><button class="btn btn-secondary btn-sm" onclick="editEntry('${e.id}')">✏️</button><button class="btn btn-danger btn-sm" onclick="deleteEntry('${e.id}')">🗑️</button></td></tr>`;
    }).join("");
  }
  updateKPIs(); updateHighlight(); updateCharts();
}
function refreshTeamlidFilter(){
  const sel=document.getElementById('filterTeamlid'); const current=sel.value;
  const names=[...new Set(entries.map(e=>e.teamlid))].sort();
  sel.innerHTML='<option value="">Alle teamleden</option>'+names.map(n=>`<option value="${n}">${n}</option>`).join("");
  sel.value=current;
}
function refreshChartTeamlidFilter(){
  const sel=document.getElementById('chartTeamlidFilter'); const current=sel.value;
  const names=[...new Set(entries.map(e=>e.teamlid))].sort();
  sel.innerHTML='<option value="">Alle teamleden</option>'+names.map(n=>`<option value="${n}">${n}</option>`).join("");
  sel.value=current;
}
function resetChartFilter(){
  document.getElementById('chartTeamlidFilter').value="";
  updateCharts();
}
function updateKPIs(){
  const today=new Date().toISOString().slice(0,10);
  document.getElementById('kpi-teamleden').textContent=new Set(entries.map(e=>e.teamlid)).size;
  document.getElementById('kpi-actief').textContent=entries.filter(e=>{const st=getStatus(e);return st.label==="Actief"||st.label==="Actief in realisatie"||st.label==="Loopt af";}).length;
  const bouwteamProjects = new Set(entries.filter(e=>BOUWTEAM_FASES.includes(e.fase)).map(e=>e.project));
  const realisatieProjects = new Set(entries.filter(e=>e.fase==="Realisatie").map(e=>e.project));
  document.getElementById('kpi-bouwteam').textContent = bouwteamProjects.size;
  document.getElementById('kpi-realisatie').textContent = realisatieProjects.size;
  document.getElementById('kpi-week').textContent=entries.filter(e=>{if(!e.einddatum) return false;const diff=(new Date(e.einddatum)-new Date(today))/(1000*60*60*24);return diff>=0 && diff<=7;}).length;
}
function updateHighlight(){
  const today=new Date().toISOString().slice(0,10);
  const items=entries.filter(e=>{if(!e.einddatum) return false;const diff=(new Date(e.einddatum)-new Date(today))/(1000*60*60*24);return diff>=0 && diff<=7;});
  const sec=document.getElementById('highlightSection'), list=document.getElementById('highlightList');
  if(items.length===0){sec.style.display="none";return;}
  sec.style.display="block";
  list.innerHTML=items.map(e=>`<div class="highlight-item"><strong>${escapeHtml(e.teamlid)}</strong>${escapeHtml(e.project)}<br><small>${e.fase} · eindigt ${fmtDate(e.einddatum)}</small></div>`).join("");
}
function updateCharts(){
  const chartTeamlidFilter = document.getElementById('chartTeamlidFilter').value;
  const chartData = chartTeamlidFilter
    ? entries.filter(e=>e.teamlid===chartTeamlidFilter)
    : entries;

  // Update subtitles + hint
  const subtitleText = chartTeamlidFilter ? `Gefilterd op: ${chartTeamlidFilter}` : "Alle teamleden";
  document.getElementById('chartFaseSubtitle').textContent = subtitleText;
  document.getElementById('chartTeamlidSubtitle').textContent = subtitleText;
  document.getElementById('chartFilterHint').textContent = chartTeamlidFilter
    ? `Toont ${chartData.length} toewijzing(en) van ${chartTeamlidFilter}`
    : "Toont data van alle teamleden";

  // Fase distribution
  const faseCount={}; FASES.forEach(f=>faseCount[f]=0);
  chartData.forEach(e=>{if(faseCount[e.fase]!==undefined) faseCount[e.fase]++;});

  // Team distribution
  const teamCount={};
  chartData.forEach(e=>{teamCount[e.teamlid]=(teamCount[e.teamlid]||0)+1;});

  if(chartFase) chartFase.destroy();
  chartFase=new Chart(document.getElementById('chartFase'),{
    type:'doughnut',
    data:{labels:Object.keys(faseCount),datasets:[{data:Object.values(faseCount),backgroundColor:['#4A90E2','#F59E0B','#8B5CF6','#10B981']}]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{position:'bottom'}}}
  });

  if(chartTeamlid) chartTeamlid.destroy();
  chartTeamlid=new Chart(document.getElementById('chartTeamlid'),{
    type:'bar',
    data:{labels:Object.keys(teamCount),datasets:[{label:'Aantal',data:Object.values(teamCount),backgroundColor:'#1F4E78'}]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{y:{beginAtZero:true,ticks:{stepSize:1}}}}
  });
}
function openModal(){document.getElementById('modalTitle').textContent="Nieuwe toewijzing";document.getElementById('entryForm').reset();document.getElementById('entryId').value="";document.getElementById('modal').classList.add('active');}
function closeModal(){document.getElementById('modal').classList.remove('active');}
function editEntry(id){const e=entries.find(x=>x.id===id);if(!e) return;document.getElementById('modalTitle').textContent="Toewijzing bewerken";document.getElementById('entryId').value=e.id;document.getElementById('teamlid').value=e.teamlid;document.getElementById('project').value=e.project;document.getElementById('fase').value=e.fase;document.getElementById('einddatum').value=e.einddatum||"";document.getElementById('startRealisatie').value=e.startRealisatie||"";document.getElementById('opmerkingen').value=e.opmerkingen||"";document.getElementById('modal').classList.add('active');}
function saveEntry(ev){
  ev.preventDefault();
  const id=document.getElementById('entryId').value;
  const data={teamlid:document.getElementById('teamlid').value.trim(),project:document.getElementById('project').value,fase:document.getElementById('fase').value,einddatum:document.getElementById('einddatum').value,startRealisatie:document.getElementById('startRealisatie').value,opmerkingen:document.getElementById('opmerkingen').value.trim()};
  if(id){const i=entries.findIndex(e=>e.id===id);if(i>=0) entries[i]={...entries[i],...data};}
  else{entries.push({id:crypto.randomUUID(),...data});}
  saveData(); closeModal(); render();
}
function deleteEntry(id){if(!confirm("Weet je zeker dat je deze toewijzing wilt verwijderen?")) return;entries=entries.filter(e=>e.id!==id);saveData();render();}
function exportData(){const blob=new Blob([JSON.stringify(entries,null,2)],{type:"application/json"});const url=URL.createObjectURL(blob);const a=document.createElement('a');a.href=url;a.download=`team-dashboard-${new Date().toISOString().slice(0,10)}.json`;a.click();URL.revokeObjectURL(url);}
function importData(ev){const file=ev.target.files[0];if(!file) return;const reader=new FileReader();reader.onload=e=>{try{const data=JSON.parse(e.target.result);if(!Array.isArray(data)) throw new Error("Ongeldig formaat");if(confirm(`Importeren van ${data.length} items? Bestaande data wordt vervangen.`)){entries=data.map(x=>({id:x.id||crypto.randomUUID(),...x}));saveData();render();}}catch(err){alert("Fout bij importeren: "+err.message);}};reader.readAsText(file);ev.target.value="";}
function escapeHtml(s){return String(s||"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));}
document.addEventListener('DOMContentLoaded',init);
document.addEventListener('click',e=>{if(e.target.id==='modal') closeModal();});
</script>
</body>
</html>
