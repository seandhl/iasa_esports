<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>내전 픽 트래커 — BO5 하드 피어리스</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Black+Han+Sans&family=Noto+Sans+KR:wght@300;400;500;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0c10;
    --bg2: #10131a;
    --bg3: #161b24;
    --surface: #1a2030;
    --surface2: #202840;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.14);
    --text: #e8eaf0;
    --text2: #8892a4;
    --text3: #505a6e;
    --gold: #c89b3c;
    --gold-light: #f0d070;
    --gold-dim: rgba(200,155,60,0.15);
    --blue: #1a78c2;
    --blue-light: #4fc3f7;
    --blue-dim: rgba(26,120,194,0.18);
    --red: #c0392b;
    --red-light: #ef5350;
    --red-dim: rgba(192,57,43,0.18);
    --top: #4fc3f7;
    --jg: #66bb6a;
    --mid: #ab47bc;
    --adc: #ef5350;
    --sup: #ffa726;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Noto Sans KR', sans-serif;
    font-size: 14px;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Background hexagon pattern */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(200,155,60,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(200,155,60,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .app {
    position: relative;
    z-index: 1;
    max-width: 1200px;
    margin: 0 auto;
    padding: 24px 20px 40px;
  }

  /* ===== HEADER ===== */
  .header {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    margin-bottom: 28px;
    padding-bottom: 20px;
    border-bottom: 1px solid var(--border);
    position: relative;
  }
  .header::after {
    content: '';
    position: absolute;
    bottom: -1px;
    left: 0;
    width: 120px;
    height: 1px;
    background: var(--gold);
  }

  .header-left {}
  .logo {
    font-family: 'Black Han Sans', sans-serif;
    font-size: 26px;
    color: var(--gold);
    letter-spacing: 0.04em;
    text-shadow: 0 0 30px rgba(200,155,60,0.4);
    line-height: 1;
  }
  .logo span {
    color: var(--text);
    font-size: 15px;
    font-family: 'Noto Sans KR', sans-serif;
    font-weight: 300;
    letter-spacing: 0.12em;
    display: block;
    margin-top: 4px;
  }
  .header-meta {
    font-size: 12px;
    color: var(--text3);
    letter-spacing: 0.08em;
    text-transform: uppercase;
  }

  .btn-reset-all {
    background: transparent;
    border: 1px solid var(--red);
    color: var(--red-light);
    font-family: 'Noto Sans KR', sans-serif;
    font-size: 12px;
    font-weight: 500;
    padding: 8px 18px;
    cursor: pointer;
    letter-spacing: 0.06em;
    transition: background 0.2s, box-shadow 0.2s;
    clip-path: polygon(6px 0%, 100% 0%, calc(100% - 6px) 100%, 0% 100%);
  }
  .btn-reset-all:hover {
    background: var(--red-dim);
    box-shadow: 0 0 16px rgba(192,57,43,0.3);
  }

  /* ===== SET TABS ===== */
  .set-tabs {
    display: flex;
    gap: 6px;
    margin-bottom: 20px;
  }
  .set-tab {
    flex: 1;
    background: var(--surface);
    border: 1px solid var(--border);
    color: var(--text2);
    font-family: 'Noto Sans KR', sans-serif;
    font-size: 13px;
    font-weight: 500;
    padding: 10px 0;
    cursor: pointer;
    transition: all 0.2s;
    position: relative;
    letter-spacing: 0.04em;
  }
  .set-tab::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 2px;
    background: var(--gold);
    transform: scaleX(0);
    transition: transform 0.2s;
  }
  .set-tab.active {
    background: var(--surface2);
    color: var(--gold);
    border-color: rgba(200,155,60,0.4);
  }
  .set-tab.active::after { transform: scaleX(1); }
  .set-tab:hover:not(.active) { background: var(--surface2); color: var(--text); }

  .set-tab .dot-row {
    display: flex;
    justify-content: center;
    gap: 3px;
    margin-top: 4px;
  }
  .dot {
    width: 5px; height: 5px;
    border-radius: 50%;
    background: var(--border2);
  }
  .dot.my { background: var(--blue-light); }
  .dot.opp { background: var(--red-light); }

  /* ===== LAYOUT ===== */
  .main-layout {
    display: grid;
    grid-template-columns: 1fr 300px;
    gap: 16px;
    align-items: start;
  }

  /* ===== LANES ===== */
  .lanes { display: flex; flex-direction: column; gap: 10px; }

  .lane-block {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 2px;
    padding: 14px 16px;
    transition: border-color 0.2s;
  }
  .lane-block:hover { border-color: var(--border2); }

  .lane-header {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 12px;
  }

  .lane-badge {
    font-family: 'Black Han Sans', sans-serif;
    font-size: 11px;
    letter-spacing: 0.1em;
    padding: 3px 10px;
    border-radius: 1px;
    text-transform: uppercase;
  }

  .lane-badge.top    { background: rgba(79,195,247,0.15); color: var(--top); border: 1px solid rgba(79,195,247,0.3); }
  .lane-badge.jg     { background: rgba(102,187,106,0.15); color: var(--jg); border: 1px solid rgba(102,187,106,0.3); }
  .lane-badge.mid    { background: rgba(171,71,188,0.15); color: var(--mid); border: 1px solid rgba(171,71,188,0.3); }
  .lane-badge.adc    { background: rgba(239,83,80,0.15); color: var(--adc); border: 1px solid rgba(239,83,80,0.3); }
  .lane-badge.sup    { background: rgba(255,167,38,0.15); color: var(--sup); border: 1px solid rgba(255,167,38,0.3); }

  .lane-name {
    font-size: 12px;
    font-weight: 700;
    color: var(--text2);
    letter-spacing: 0.06em;
  }

  .champs {
    display: flex;
    flex-wrap: wrap;
    gap: 5px;
  }

  .champ-btn {
    font-family: 'Noto Sans KR', sans-serif;
    font-size: 12px;
    font-weight: 400;
    padding: 4px 12px;
    background: var(--bg3);
    border: 1px solid var(--border);
    color: var(--text2);
    cursor: pointer;
    border-radius: 1px;
    transition: all 0.15s;
    white-space: nowrap;
    clip-path: polygon(4px 0%, 100% 0%, calc(100% - 4px) 100%, 0% 100%);
  }
  .champ-btn:hover:not(.used):not(.sel-my):not(.sel-opp) {
    background: var(--surface2);
    color: var(--text);
    border-color: var(--border2);
  }
  .champ-btn.sel-my {
    background: var(--blue-dim);
    border-color: var(--blue);
    color: var(--blue-light);
    font-weight: 500;
    box-shadow: 0 0 8px rgba(26,120,194,0.25);
  }
  .champ-btn.sel-opp {
    background: var(--red-dim);
    border-color: var(--red);
    color: var(--red-light);
    font-weight: 500;
    box-shadow: 0 0 8px rgba(192,57,43,0.25);
  }
  .champ-btn.used {
    opacity: 0.22;
    text-decoration: line-through;
    cursor: not-allowed;
    pointer-events: none;
  }

  /* ===== SIDE PANEL ===== */
  .side-panel {
    position: sticky;
    top: 20px;
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  /* Team toggle */
  .team-toggle-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 0;
    border: 1px solid var(--border);
    border-radius: 2px;
    overflow: hidden;
  }
  .team-btn {
    background: var(--surface);
    border: none;
    color: var(--text2);
    font-family: 'Noto Sans KR', sans-serif;
    font-size: 13px;
    font-weight: 500;
    padding: 11px 0;
    cursor: pointer;
    transition: all 0.18s;
    letter-spacing: 0.04em;
  }
  .team-btn:first-child { border-right: 1px solid var(--border); }
  .team-btn.active-my {
    background: var(--blue-dim);
    color: var(--blue-light);
    border-color: var(--blue);
    box-shadow: inset 0 -2px 0 var(--blue);
  }
  .team-btn.active-opp {
    background: var(--red-dim);
    color: var(--red-light);
    border-color: var(--red);
    box-shadow: inset 0 -2px 0 var(--red);
  }
  .team-btn:hover:not(.active-my):not(.active-opp) {
    background: var(--surface2);
    color: var(--text);
  }

  /* Picks card */
  .picks-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 2px;
    overflow: hidden;
  }
  .picks-card-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 16px;
    border-bottom: 1px solid var(--border);
    background: var(--bg3);
  }
  .picks-card-title {
    font-family: 'Black Han Sans', sans-serif;
    font-size: 14px;
    color: var(--gold);
    letter-spacing: 0.06em;
  }
  .btn-reset-set {
    background: transparent;
    border: 1px solid var(--border2);
    color: var(--text2);
    font-family: 'Noto Sans KR', sans-serif;
    font-size: 11px;
    padding: 4px 10px;
    cursor: pointer;
    transition: all 0.15s;
    letter-spacing: 0.04em;
  }
  .btn-reset-set:hover { border-color: var(--gold); color: var(--gold); }

  .picks-body { padding: 10px 16px; }

  .pick-lane-block {
    padding: 8px 0;
    border-bottom: 1px solid var(--border);
  }
  .pick-lane-block:last-child { border-bottom: none; }

  .pick-lane-label {
    font-size: 10px;
    font-weight: 700;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text3);
    margin-bottom: 5px;
  }

  .pick-entries {
    display: flex;
    flex-direction: column;
    gap: 3px;
  }

  .pick-entry {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .pick-team-tag {
    font-size: 10px;
    font-weight: 700;
    letter-spacing: 0.06em;
    padding: 1px 6px;
    border-radius: 1px;
    min-width: 38px;
    text-align: center;
  }
  .pick-team-tag.my  { background: var(--blue-dim); color: var(--blue-light); border: 1px solid rgba(79,195,247,0.3); }
  .pick-team-tag.opp { background: var(--red-dim); color: var(--red-light); border: 1px solid rgba(239,83,80,0.3); }

  .pick-champ-name {
    font-size: 13px;
    font-weight: 500;
    color: var(--text);
  }
  .pick-champ-name.empty {
    color: var(--text3);
    font-style: italic;
    font-weight: 300;
    font-size: 12px;
  }

  /* Stats row */
  .stats-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
  }
  .stat-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 2px;
    padding: 10px 12px;
    text-align: center;
  }
  .stat-num {
    font-family: 'Black Han Sans', sans-serif;
    font-size: 22px;
    color: var(--gold);
    line-height: 1;
  }
  .stat-label {
    font-size: 10px;
    color: var(--text3);
    letter-spacing: 0.08em;
    margin-top: 3px;
    text-transform: uppercase;
  }

  /* Status bar */
  .status-bar {
    font-size: 11px;
    color: var(--text3);
    letter-spacing: 0.06em;
    text-align: center;
    padding: 8px 0;
    border-top: 1px solid var(--border);
    margin-top: 4px;
  }

  /* Legend */
  .legend {
    display: flex;
    align-items: center;
    gap: 16px;
    margin-bottom: 14px;
  }
  .legend-item {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 11px;
    color: var(--text2);
    letter-spacing: 0.04em;
  }
  .legend-pip {
    width: 10px;
    height: 10px;
    border-radius: 1px;
  }

  .current-pick-badge {
    margin-left: auto;
    font-size: 11px;
    padding: 2px 10px;
    border-radius: 1px;
    background: var(--blue-dim);
    color: var(--blue-light);
    border: 1px solid var(--blue);
    font-weight: 500;
    letter-spacing: 0.04em;
  }

  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(6px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .lane-block { animation: fadeIn 0.3s ease both; }
  .lane-block:nth-child(1) { animation-delay: 0.05s; }
  .lane-block:nth-child(2) { animation-delay: 0.10s; }
  .lane-block:nth-child(3) { animation-delay: 0.15s; }
  .lane-block:nth-child(4) { animation-delay: 0.20s; }
  .lane-block:nth-child(5) { animation-delay: 0.25s; }
</style>
</head>
<body>
<div class="app">

  <!-- HEADER -->
  <div class="header">
    <div class="header-left">
      <div class="logo">내전 픽 트래커<span>BO5 HARD FEARLESS</span></div>
    </div>
    <div style="display:flex;align-items:center;gap:16px;">
      <div class="header-meta" id="header-meta">세트 1 진행중</div>
      <button class="btn-reset-all" onclick="resetAll()">전체 초기화</button>
    </div>
  </div>

  <!-- SET TABS -->
  <div class="set-tabs" id="set-tabs"></div>

  <!-- STATS ROW -->
  <div class="stats-row" style="margin-bottom:16px;">
    <div class="stat-box">
      <div class="stat-num" id="stat-used">0</div>
      <div class="stat-label">사용된 챔피언</div>
    </div>
    <div class="stat-box">
      <div class="stat-num" id="stat-remaining">—</div>
      <div class="stat-label">남은 픽 슬롯</div>
    </div>
  </div>

  <!-- LEGEND -->
  <div class="legend">
    <div class="legend-item">
      <div class="legend-pip" style="background:var(--blue-dim);border:1px solid var(--blue);"></div>
      우리팀 픽
    </div>
    <div class="legend-item">
      <div class="legend-pip" style="background:var(--red-dim);border:1px solid var(--red);"></div>
      상대팀 픽
    </div>
    <div class="legend-item">
      <div class="legend-pip" style="background:rgba(255,255,255,0.06);border:1px solid rgba(255,255,255,0.08);opacity:0.4;"></div>
      이미 사용됨
    </div>
  </div>

  <!-- MAIN LAYOUT -->
  <div class="main-layout">
    <div class="lanes" id="lanes-container"></div>

    <div class="side-panel">
      <div class="team-toggle-row">
        <button class="team-btn active-my" onclick="setTeam('my')">우리팀</button>
        <button class="team-btn" onclick="setTeam('opp')">상대팀</button>
      </div>

      <div class="picks-card">
        <div class="picks-card-header">
          <div class="picks-card-title" id="picks-title">세트 1 조합</div>
          <button class="btn-reset-set" onclick="resetCurrentSet()">이 세트 초기화</button>
        </div>
        <div class="picks-body" id="picks-body"></div>
        <div class="status-bar" id="status-bar">우리팀 픽 선택 중 — 라인 챔피언을 클릭하세요</div>
      </div>
    </div>
  </div>
</div>

<script>
// ── 우리팀 라인별 픽 풀 ──────────────────────────────────────────
const LANES = [
  { id:'top', label:'TOP', name:'탑', cls:'top', champs:['모데카이저','카밀','오른','문도 박사','말파이트','럼블','요릭','자헨','쉔','사이온','가렌'] },
  { id:'jg',  label:'JG',  name:'정글', cls:'jg', champs:['바이','오공','비에고','나피리','리신','릴리아','신짜오','녹턴','사일러스','판테온','자르반','세주아니','마오카이'] },
  { id:'mid', label:'MID', name:'미드', cls:'mid', champs:['갈리오','사이온','세주아니','마오카이','다이애나','나피리','아칼리','아리','리산드라','요네','모르가나'] },
  { id:'adc', label:'ADC', name:'원딜', cls:'adc', champs:['카이사','징크스','애쉬','자야','이즈리얼','코르키','트리스타나','베인','시비르','미스포춘','스몰더','진','루시안'] },
  { id:'sup', label:'SUP', name:'서폿', cls:'sup', champs:['노틸러스','카르마','룰루','알리스타','바드','라칸','레오나','나미','브라움','렐','탐켄치','쓰레쉬','밀리오','니코'] },
];

// ── 상대팀 전체 챔피언 풀 (라인 무관) ────────────────────────────
const ALL_CHAMPS = ['가렌','갈리오','갱플랭크','그라가스','그레이브즈','나르','나미','나서스','나피리','노틸러스','녹턴','다리우스','다이애나','드레이븐','람머스','라칸','럭스','럼블','레넥톤','레오나','렉사이','렝가','루시안','룰루','르블랑','리 신','리반','리산드라','말자하','말파이트','마오카이','마스터 이','모데카이저','모르가나','문도 박사','미스 포츈','바드','바루스','바이','베이가','베인','벨코즈','볼리베어','브라움','브랜드','블라디미르','블리츠크랭크','빅토르','뽀삐','사이온','샤코','세주아니','소나','소라카','스웨인','스카너','신드라','신 짜오','신지드','아무무','아리','아우렐리온 솔','아이번','아지르','아칼리','아트록스','알리스타','애니','애니비아','애쉬','야스오','에코','에코','엘리스','오공','오른','오리아나','올라프','우디르','우르곳','워윅','요네','요릭','이렐리아','이블린','이즈리얼','일라오이','자르반 4세','자야','자이라','자크','자헨','잔나','잭스','제드','제라스','제이스','직스','진','징크스','질리언','초가스','카르마','카밀','카사딘','카서스','카시오페아','카이사','카직스','칼리스타','케네','케닌','케일','케이틀린','코그모','코르키','퀸','클레드','킨드레드','타릭','탈론','탈리야','탐 켄치','트리스타나','트린다미어','트런들','트위스티드 페이트','트위치','티모','판테온','피들스틱','피오라','피즈','하이머딩거','헤카림','쉔','쉬바나','쓰레쉬','렐','룰루','누누','뤄','럼블','리 신','리반','모르가나','문도 박사','바이','볼리베어','사일러스','세주아니','신짜오','아리','아칼리','오공','이즈리얼','자르반','자야','진','징크스','코르키','트리스타나','베인','시비르','미스포춘','카이사','나피리','릴리아','마오카이','녹턴','판테온','갈리오','다이애나','리산드라','요네','노틸러스','카르마','알리스타','바드','라칸','레오나','나미','브라움','탐켄치','밀리오','니코','스몰더','루시안'].filter((v,i,a)=>a.indexOf(v)===i).sort();

const LANE_IDS = LANES.map(l=>l.id);
const NUM_SETS = 5;

let currentSet = 0;
let currentTeam = 'my';
let oppSearch = '';
let oppSelectedLane = null; // which lane slot to fill for opp
let picks = makePicks();

function makePicks() {
  return Array.from({length:NUM_SETS}, ()=>({
    my:  Object.fromEntries(LANE_IDS.map(id=>[id,null])),
    opp: Object.fromEntries(LANE_IDS.map(id=>[id,null])),
  }));
}

function getAllUsed() {
  const s = new Set();
  picks.forEach(p=>['my','opp'].forEach(t=>LANE_IDS.forEach(lid=>{ if(p[t][lid]) s.add(p[t][lid]); })));
  return s;
}

function getRemainingSlots() {
  let filled = 0;
  picks.forEach(p=>['my','opp'].forEach(t=>LANE_IDS.forEach(lid=>{ if(p[t][lid]) filled++; })));
  return NUM_SETS * LANE_IDS.length * 2 - filled;
}

// ── 우리팀: 라인 버튼 클릭 ───────────────────────────────────────
function handleMyChamp(laneId, champ) {
  const allUsed = getAllUsed();
  const myP = picks[currentSet].my[laneId];
  if (myP === champ) { picks[currentSet].my[laneId] = null; }
  else if (allUsed.has(champ) && myP !== champ) return;
  else picks[currentSet].my[laneId] = champ;
  render();
}

// ── 상대팀: 라인 슬롯 선택 ──────────────────────────────────────
function selectOppLane(laneId) {
  oppSelectedLane = (oppSelectedLane === laneId) ? null : laneId;
  render();
}

// ── 상대팀: 전체 풀에서 챔피언 선택 ────────────────────────────
function handleOppChamp(champ) {
  if (!oppSelectedLane) return;
  const allUsed = getAllUsed();
  const opP = picks[currentSet].opp[oppSelectedLane];
  if (opP === champ) {
    picks[currentSet].opp[oppSelectedLane] = null;
  } else if (allUsed.has(champ) && opP !== champ) return;
  else {
    picks[currentSet].opp[oppSelectedLane] = champ;
    oppSelectedLane = null; // auto-close lane after pick
  }
  render();
}

function setTeam(t) {
  currentTeam = t;
  oppSelectedLane = null;
  document.querySelectorAll('.team-btn').forEach(b=>{
    b.className='team-btn';
    if(b.textContent==='우리팀'&&t==='my') b.classList.add('active-my');
    if(b.textContent==='상대팀'&&t==='opp') b.classList.add('active-opp');
  });
  document.getElementById('status-bar').textContent = t==='my' ? '우리팀 픽 선택 중' : '상대팀: 라인 선택 후 챔피언을 고르세요';
  render();
}

function resetCurrentSet() {
  picks[currentSet] = {
    my:  Object.fromEntries(LANE_IDS.map(id=>[id,null])),
    opp: Object.fromEntries(LANE_IDS.map(id=>[id,null])),
  };
  oppSelectedLane = null;
  render();
}

function resetAll() {
  picks = makePicks();
  currentSet = 0;
  currentTeam = 'my';
  oppSelectedLane = null;
  oppSearch = '';
  document.querySelectorAll('.team-btn').forEach(b=>{
    b.className='team-btn';
    if(b.textContent==='우리팀') b.classList.add('active-my');
  });
  render();
}

// ── RENDER: 세트 탭 ─────────────────────────────────────────────
function renderTabs() {
  const el = document.getElementById('set-tabs');
  el.innerHTML='';
  for(let i=0;i<NUM_SETS;i++){
    const myDots = LANE_IDS.map(lid=>picks[i].my[lid]?'my':'').filter(Boolean);
    const opDots = LANE_IDS.map(lid=>picks[i].opp[lid]?'opp':'').filter(Boolean);
    const allDots = [...myDots,...opDots].slice(0,10);
    const t=document.createElement('button');
    t.className='set-tab'+(i===currentSet?' active':'');
    t.innerHTML=`세트 ${i+1}<div class="dot-row">${allDots.map(d=>`<div class="dot ${d}"></div>`).join('')}${Array(10-allDots.length).fill('<div class="dot"></div>').join('')}</div>`;
    t.onclick=()=>{ currentSet=i; oppSelectedLane=null; render(); };
    el.appendChild(t);
  }
}

// ── RENDER: 우리팀 라인 블록 ────────────────────────────────────
function renderMyLanes() {
  const allUsed = getAllUsed();
  const container = document.getElementById('lanes-container');
  container.innerHTML='';
  LANES.forEach(lane=>{
    const myP = picks[currentSet].my[lane.id];
    const block = document.createElement('div');
    block.className='lane-block';
    const champsHTML = lane.champs.map(champ=>{
      const isMy = myP===champ;
      const isUsed = allUsed.has(champ)&&!isMy;
      let cls='champ-btn';
      if(isMy) cls+=' sel-my';
      else if(isUsed) cls+=' used';
      return `<button class="${cls}" onclick="handleMyChamp('${lane.id}','${champ.replace(/'/g,"\\'")}')">${champ}</button>`;
    }).join('');
    block.innerHTML=`
      <div class="lane-header">
        <div class="lane-badge ${lane.cls}">${lane.label}</div>
        <div class="lane-name">${lane.name}</div>
        ${myP?`<div class="current-pick-badge">${myP}</div>`:''}
      </div>
      <div class="champs">${champsHTML}</div>
    `;
    container.appendChild(block);
  });
}

// ── RENDER: 상대팀 라인 슬롯 + 전체 챔피언 풀 ──────────────────
function renderOppLanes() {
  const allUsed = getAllUsed();
  const container = document.getElementById('lanes-container');
  container.innerHTML='';

  // Lane slot buttons (5 clickable slots)
  const slotDiv = document.createElement('div');
  slotDiv.className='lane-block';
  slotDiv.innerHTML=`<div class="lane-header" style="margin-bottom:10px;"><div class="lane-name" style="font-size:13px;color:var(--text2);">라인 슬롯 선택</div></div>`;
  const slotsRow = document.createElement('div');
  slotsRow.style.cssText='display:flex;gap:8px;flex-wrap:wrap;';
  LANES.forEach(lane=>{
    const opP = picks[currentSet].opp[lane.id];
    const isActive = oppSelectedLane === lane.id;
    const btn = document.createElement('button');
    btn.style.cssText=`flex:1;min-width:80px;padding:10px 8px;border-radius:2px;border:1px solid ${isActive?'var(--red)':'var(--border)'};background:${isActive?'var(--red-dim)':'var(--surface)'};color:${isActive?'var(--red-light)':'var(--text2)'};font-family:'Noto Sans KR',sans-serif;font-size:12px;font-weight:500;cursor:pointer;transition:all 0.15s;text-align:center;`;
    btn.innerHTML=`<div style="font-family:'Black Han Sans',sans-serif;letter-spacing:0.08em;font-size:11px;margin-bottom:3px;">${lane.label}</div><div style="font-size:11px;color:${opP?(isActive?'var(--red-light)':'var(--text)'):'var(--text3)'}">${opP||'미선택'}</div>`;
    btn.onclick=()=>selectOppLane(lane.id);
    slotsRow.appendChild(btn);
  });
  slotDiv.appendChild(slotsRow);
  container.appendChild(slotDiv);

  if (!oppSelectedLane) {
    const hint = document.createElement('div');
    hint.style.cssText='text-align:center;padding:30px;color:var(--text3);font-size:13px;letter-spacing:0.04em;';
    hint.textContent='위 라인 슬롯을 클릭하면 전체 챔피언 목록이 나타납니다';
    container.appendChild(hint);
    return;
  }

  // Full champion pool
  const poolBlock = document.createElement('div');
  poolBlock.className='lane-block';
  const query = oppSearch.trim();
  const filtered = ALL_CHAMPS.filter(c=> !query || c.includes(query));
  const selLane = LANES.find(l=>l.id===oppSelectedLane);

  poolBlock.innerHTML=`
    <div class="lane-header" style="margin-bottom:10px;">
      <div class="lane-badge ${selLane.cls}">${selLane.label} 적군 픽 선택</div>
      <div style="flex:1;"></div>
      <input id="opp-search" type="text" placeholder="챔피언 검색..." value="${oppSearch}"
        style="background:var(--bg3);border:1px solid var(--border);color:var(--text);font-family:'Noto Sans KR',sans-serif;font-size:12px;padding:5px 10px;border-radius:2px;width:160px;outline:none;"
        oninput="oppSearch=this.value;renderOppLanes();"
      />
    </div>
    <div class="champs" id="opp-pool"></div>
  `;
  container.appendChild(poolBlock);

  const pool = poolBlock.querySelector('#opp-pool');
  const opP = picks[currentSet].opp[oppSelectedLane];
  filtered.forEach(champ=>{
    const isSel = opP===champ;
    const isUsed = allUsed.has(champ) && !isSel;
    let cls='champ-btn';
    if(isSel) cls+=' sel-opp';
    else if(isUsed) cls+=' used';
    const btn = document.createElement('button');
    btn.className=cls;
    btn.textContent=champ;
    btn.onclick=()=>handleOppChamp(champ);
    pool.appendChild(btn);
  });

  if(filtered.length===0){
    pool.innerHTML=`<span style="color:var(--text3);font-size:12px;">"${oppSearch}" 결과 없음</span>`;
  }
}

// ── RENDER: 조합 패널 ───────────────────────────────────────────
function renderPicks() {
  const body = document.getElementById('picks-body');
  body.innerHTML = LANES.map(lane=>{
    const myP = picks[currentSet].my[lane.id];
    const opP = picks[currentSet].opp[lane.id];
    return `
      <div class="pick-lane-block">
        <div class="pick-lane-label">${lane.label} — ${lane.name}</div>
        <div class="pick-entries">
          <div class="pick-entry">
            <div class="pick-team-tag my">아군</div>
            <div class="pick-champ-name${myP?'':' empty'}">${myP||'미선택'}</div>
          </div>
          <div class="pick-entry">
            <div class="pick-team-tag opp">적군</div>
            <div class="pick-champ-name${opP?'':' empty'}">${opP||'미선택'}</div>
          </div>
        </div>
      </div>`;
  }).join('');
  document.getElementById('picks-title').textContent=`세트 ${currentSet+1} 조합`;
}

function renderStats() {
  const allUsed = getAllUsed();
  document.getElementById('stat-used').textContent = allUsed.size;
  document.getElementById('stat-remaining').textContent = getRemainingSlots();
  document.getElementById('header-meta').textContent = `세트 ${currentSet+1} 진행중`;
}

function render() {
  renderTabs();
  if (currentTeam === 'my') renderMyLanes();
  else renderOppLanes();
  renderPicks();
  renderStats();
}

render();
</script>
</body>
</html>
