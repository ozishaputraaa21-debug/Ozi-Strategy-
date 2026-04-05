<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Portfolio">
<meta name="theme-color" content="#0a0c0f">
<title>Portfolio Dividen</title>

<!-- PWA Manifest inline -->
<script>
const manifestData = {
  name: "Portfolio Dividen",
  short_name: "Portfolio",
  description: "Dashboard portfolio dividen dengan proyeksi compound 10 tahun",
  start_url: ".",
  display: "standalone",
  background_color: "#0a0c0f",
  theme_color: "#0a0c0f",
  orientation: "portrait-primary",
  icons: [
    { src: "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 192 192'><rect width='192' height='192' rx='32' fill='%230a0c0f'/><text y='130' x='96' text-anchor='middle' font-size='110' font-family='serif'>📈</text></svg>", sizes: "192x192", type: "image/svg+xml" },
    { src: "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'><rect width='512' height='512' rx='80' fill='%230a0c0f'/><text y='360' x='256' text-anchor='middle' font-size='300' font-family='serif'>📈</text></svg>", sizes: "512x512", type: "image/svg+xml" }
  ]
};
const blob = new Blob([JSON.stringify(manifestData)], {type:'application/json'});
const manifestURL = URL.createObjectURL(blob);
document.write('<link rel="manifest" href="'+manifestURL+'">');
</script>

<!-- Service Worker inline -->
<script>
if ('serviceWorker' in navigator) {
  const swCode = `
    const CACHE = 'portfolio-v1';
    self.addEventListener('install', e => {
      self.skipWaiting();
    });
    self.addEventListener('activate', e => {
      e.waitUntil(clients.claim());
    });
    self.addEventListener('fetch', e => {
      e.respondWith(
        caches.match(e.request).then(cached => {
          if (cached) return cached;
          return fetch(e.request).then(res => {
            const clone = res.clone();
            caches.open(CACHE).then(c => c.put(e.request, clone));
            return res;
          }).catch(() => cached);
        })
      );
    });
  `;
  const swBlob = new Blob([swCode], {type:'application/javascript'});
  const swURL = URL.createObjectURL(swBlob);
  navigator.serviceWorker.register(swURL).catch(()=>{});
}
</script>

<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700;900&family=DM+Mono:wght@300;400;500&family=Instrument+Sans:wght@300;400;500&display=swap" rel="stylesheet">

<style>
:root {
  --bg: #0a0c0f;
  --surface: #111318;
  --border: #1e2230;
  --text: #e8e6df;
  --muted: #6b6f7a;
  --core: #c8a96e;
  --core-dim: rgba(200,169,110,0.12);
  --agresif: #e05c4b;
  --agresif-dim: rgba(224,92,75,0.12);
  --growth: #4b9e7e;
  --growth-dim: rgba(75,158,126,0.12);
  --hedge: #7a91b8;
  --hedge-dim: rgba(122,145,184,0.12);
  --safe-top: env(safe-area-inset-top, 0px);
  --safe-bot: env(safe-area-inset-bottom, 0px);
}
* { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }

html, body { height: 100%; overflow: hidden; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: 'Instrument Sans', sans-serif;
  font-weight: 300;
  display: flex;
  flex-direction: column;
}

/* ── TOP NAV ── */
.topbar {
  background: rgba(10,12,15,0.92);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  border-bottom: 1px solid var(--border);
  padding: calc(var(--safe-top) + 12px) 20px 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-shrink: 0;
  position: relative;
  z-index: 10;
}
.topbar-title { font-family: 'Playfair Display', serif; font-size: 18px; font-weight: 700; }
.topbar-sub { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; margin-top: 2px; }
.topbar-badge { text-align: right; }
.topbar-badge .tb-label { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.1em; }
.topbar-badge .tb-val { font-family: 'Playfair Display', serif; font-size: 20px; font-weight: 900; color: var(--text); letter-spacing: -1px; }

/* ── BOTTOM TABS ── */
.bottom-tabs {
  background: rgba(10,12,15,0.95);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  border-top: 1px solid var(--border);
  display: flex;
  padding-bottom: var(--safe-bot);
  flex-shrink: 0;
  z-index: 10;
}
.tab-btn {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px 4px;
  background: none;
  border: none;
  cursor: pointer;
  gap: 3px;
  position: relative;
  transition: opacity 0.15s;
}
.tab-btn .tab-icon { font-size: 20px; }
.tab-btn .tab-label { font-family: 'DM Mono', monospace; font-size: 9px; text-transform: uppercase; letter-spacing: 0.06em; color: var(--muted); transition: color 0.15s; }
.tab-btn.active .tab-label { color: var(--growth); }
.tab-btn.active::after { content: ''; position: absolute; top: 0; left: 20%; right: 20%; height: 2px; background: var(--growth); border-radius: 0 0 2px 2px; }

/* ── PAGES ── */
.pages { flex: 1; overflow: hidden; position: relative; }
.page { position: absolute; inset: 0; overflow-y: auto; -webkit-overflow-scrolling: touch; padding: 16px 16px 20px; opacity: 0; pointer-events: none; transition: opacity 0.2s; }
.page.active { opacity: 1; pointer-events: all; }

/* ── CARD ── */
.card { background: var(--surface); border: 1px solid var(--border); border-radius: 14px; overflow: hidden; margin-bottom: 14px; }

/* Donut overview */
.overview-row { display: flex; align-items: center; gap: 20px; padding: 18px; }
.donut-wrap { flex-shrink: 0; position: relative; width: 110px; height: 110px; }
.donut-wrap svg { transform: rotate(-90deg); width: 110px; height: 110px; }
.donut-center { position: absolute; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; }
.donut-center span:first-child { font-family: 'DM Mono', monospace; font-size: 8px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; }
.donut-center span:last-child { font-family: 'Playfair Display', serif; font-size: 16px; font-weight: 700; margin-top: 1px; }
.legend { flex: 1; display: flex; flex-direction: column; gap: 8px; }
.legend-item { display: flex; align-items: center; justify-content: space-between; gap: 8px; }
.legend-dot-label { display: flex; align-items: center; gap: 6px; }
.legend-dot { width: 7px; height: 7px; border-radius: 2px; flex-shrink: 0; }
.legend-name { font-size: 11px; }
.legend-pct { font-family: 'DM Mono', monospace; font-size: 11px; color: var(--muted); }

/* Stats row */
.stats-row { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; margin-bottom: 14px; }
.stat-card { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 14px; }
.stat-card .sc-label { font-family: 'DM Mono', monospace; font-size: 9px; text-transform: uppercase; letter-spacing: 0.1em; color: var(--muted); margin-bottom: 4px; }
.stat-card .sc-val { font-family: 'Playfair Display', serif; font-size: 20px; font-weight: 700; }
.stat-card .sc-sub { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--muted); margin-top: 2px; }

/* Holding list */
.section-hdr { display: flex; align-items: center; justify-content: space-between; padding: 12px 16px; border-bottom: 1px solid var(--border); }
.sh-left { display: flex; align-items: center; gap: 8px; }
.sh-pip { width: 8px; height: 8px; border-radius: 2px; }
.sh-title { font-family: 'Playfair Display', serif; font-size: 14px; font-weight: 700; }
.sh-tag { font-family: 'DM Mono', monospace; font-size: 8px; text-transform: uppercase; letter-spacing: 0.08em; padding: 2px 7px; border-radius: 20px; }
.sh-pct { font-family: 'Playfair Display', serif; font-size: 18px; font-weight: 900; }
.sh-amount { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--muted); margin-top: 1px; text-align: right; }

.holding { display: flex; align-items: center; justify-content: space-between; padding: 10px 16px; border-bottom: 1px solid rgba(30,34,48,0.7); }
.holding:last-child { border-bottom: none; }
.hn { font-size: 12px; }
.ht { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--muted); text-transform: uppercase; margin-top: 2px; }
.ha { font-family: 'DM Mono', monospace; font-size: 12px; font-weight: 500; text-align: right; }
.hb-row { display: flex; align-items: center; gap: 5px; justify-content: flex-end; margin-top: 2px; }
.hb-bg { width: 48px; height: 2px; background: var(--border); border-radius: 2px; overflow: hidden; }
.hb { height: 100%; border-radius: 2px; }
.hp { font-family: 'DM Mono', monospace; font-size: 8px; color: var(--muted); }

.fungsi { padding: 8px 16px; background: var(--bg); font-size: 10px; color: var(--muted); font-style: italic; }

/* Color themes */
.core .section-hdr { background: var(--core-dim); }
.core .sh-pip { background: var(--core); }
.core .sh-title { color: var(--core); }
.core .sh-tag { background: var(--core-dim); color: var(--core); }
.core .sh-pct { color: var(--core); }
.core .hb { background: var(--core); }
.agresif .section-hdr { background: var(--agresif-dim); }
.agresif .sh-pip { background: var(--agresif); }
.agresif .sh-title { color: var(--agresif); }
.agresif .sh-tag { background: var(--agresif-dim); color: var(--agresif); }
.agresif .sh-pct { color: var(--agresif); }
.agresif .hb { background: var(--agresif); }
.growth .section-hdr { background: var(--growth-dim); }
.growth .sh-pip { background: var(--growth); }
.growth .sh-title { color: var(--growth); }
.growth .sh-tag { background: var(--growth-dim); color: var(--growth); }
.growth .sh-pct { color: var(--growth); }
.growth .hb { background: var(--growth); }
.hedge .section-hdr { background: var(--hedge-dim); }
.hedge .sh-pip { background: var(--hedge); }
.hedge .sh-title { color: var(--hedge); }
.hedge .sh-tag { background: var(--hedge-dim); color: var(--hedge); }
.hedge .sh-pct { color: var(--hedge); }
.hedge .hb { background: var(--hedge); }

/* ── COMPOUND PAGE ── */
.compound-hero { padding: 18px; background: var(--growth-dim); border-bottom: 1px solid var(--border); }
.ch-label { font-family: 'DM Mono', monospace; font-size: 9px; text-transform: uppercase; letter-spacing: 0.1em; color: var(--muted); }
.ch-val { font-family: 'Playfair Display', serif; font-size: 30px; font-weight: 900; color: var(--growth); letter-spacing: -1.5px; margin-top: 2px; }
.ch-sub { font-family: 'DM Mono', monospace; font-size: 10px; color: var(--muted); margin-top: 3px; }

.rate-controls { display: flex; align-items: center; gap: 10px; padding: 12px 16px; border-bottom: 1px solid var(--border); flex-wrap: wrap; }
.rate-label { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; }
.rate-chips { display: flex; gap: 5px; flex-wrap: wrap; }
.rate-chip { font-family: 'DM Mono', monospace; font-size: 10px; padding: 4px 10px; border-radius: 20px; border: 1px solid var(--border); background: transparent; color: var(--muted); cursor: pointer; }
.rate-chip.active { background: var(--growth-dim); border-color: var(--growth); color: var(--growth); }
.slider-wrap { display: flex; align-items: center; gap: 6px; width: 100%; padding-top: 4px; }
.slider-wrap input[type=range] { -webkit-appearance: none; flex: 1; height: 3px; background: var(--border); border-radius: 2px; outline: none; cursor: pointer; }
.slider-wrap input[type=range]::-webkit-slider-thumb { -webkit-appearance: none; width: 16px; height: 16px; border-radius: 50%; background: var(--growth); cursor: pointer; }
.slider-val { font-family: 'DM Mono', monospace; font-size: 11px; color: var(--growth); min-width: 32px; }

.chart-wrap { padding: 14px 16px 6px; }
.chart-wrap svg { width: 100%; display: block; }

.year-table-wrap { overflow-x: auto; }
table.yt { width: 100%; border-collapse: collapse; min-width: 340px; }
table.yt th { font-family: 'DM Mono', monospace; font-size: 8px; text-transform: uppercase; letter-spacing: 0.07em; color: var(--muted); padding: 7px 12px; text-align: left; border-bottom: 1px solid var(--border); background: var(--bg); white-space: nowrap; }
table.yt th:not(:first-child) { text-align: right; }
table.yt td { font-family: 'DM Mono', monospace; font-size: 10px; padding: 8px 12px; border-bottom: 1px solid rgba(30,34,48,0.5); color: var(--muted); white-space: nowrap; }
table.yt tr:last-child td { border-bottom: none; }
table.yt td:not(:first-child) { text-align: right; }
table.yt td:first-child { color: var(--text); }
table.yt tr.hl td { background: rgba(75,158,126,0.07); }
table.yt tr.hl td:nth-child(4) { color: var(--growth); font-weight: 500; }
.ms { display: inline-block; font-size: 7px; padding: 1px 5px; border-radius: 10px; background: var(--growth-dim); color: var(--growth); margin-left: 4px; vertical-align: middle; }

.assum { display: flex; flex-wrap: wrap; gap: 10px; padding: 12px 16px; background: var(--bg); border-top: 1px solid var(--border); }
.assum-item { display: flex; align-items: center; gap: 5px; }
.assum-dot { width: 5px; height: 5px; border-radius: 50%; }
.assum-text { font-family: 'DM Mono', monospace; font-size: 8px; color: var(--muted); }

/* ── INSTALL BANNER ── */
#installBanner {
  display: none;
  position: fixed;
  bottom: calc(56px + var(--safe-bot) + 10px);
  left: 16px; right: 16px;
  background: #1a1f2e;
  border: 1px solid var(--growth);
  border-radius: 14px;
  padding: 14px 16px;
  z-index: 100;
  box-shadow: 0 8px 32px rgba(0,0,0,0.5);
  animation: slideUp 0.3s ease;
}
#installBanner.show { display: flex; align-items: center; gap: 12px; }
.ib-icon { font-size: 28px; flex-shrink: 0; }
.ib-text { flex: 1; }
.ib-title { font-size: 13px; font-weight: 500; color: var(--text); }
.ib-sub { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--muted); margin-top: 2px; }
.ib-btns { display: flex; gap: 6px; flex-direction: column; }
.ib-install { background: var(--growth); color: #fff; border: none; border-radius: 8px; padding: 7px 14px; font-family: 'DM Mono', monospace; font-size: 10px; cursor: pointer; white-space: nowrap; }
.ib-dismiss { background: transparent; color: var(--muted); border: none; font-family: 'DM Mono', monospace; font-size: 9px; cursor: pointer; text-align: center; padding: 4px; }

/* iOS install hint */
#iosBanner {
  display: none;
  position: fixed;
  bottom: calc(56px + var(--safe-bot) + 10px);
  left: 16px; right: 16px;
  background: #1a1f2e;
  border: 1px solid var(--hedge);
  border-radius: 14px;
  padding: 14px 16px;
  z-index: 100;
  box-shadow: 0 8px 32px rgba(0,0,0,0.5);
  animation: slideUp 0.3s ease;
}
#iosBanner.show { display: block; }
.ios-title { font-size: 13px; font-weight: 500; color: var(--text); margin-bottom: 6px; }
.ios-steps { font-family: 'DM Mono', monospace; font-size: 10px; color: var(--muted); line-height: 1.8; }
.ios-close { display: block; margin-top: 10px; background: transparent; border: 1px solid var(--border); border-radius: 8px; padding: 6px; width: 100%; font-family: 'DM Mono', monospace; font-size: 10px; color: var(--muted); cursor: pointer; }

@keyframes slideUp { from { opacity: 0; transform: translateY(16px); } to { opacity: 1; transform: translateY(0); } }
@keyframes fadeUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
</style>
</head>
<body>

<!-- TOP BAR -->
<div class="topbar">
  <div>
    <div class="topbar-title">Portfolio Dividen</div>
    <div class="topbar-sub">Strategi 4-Lapis · 7 Posisi</div>
  </div>
  <div class="topbar-badge">
    <div class="tb-label">Total / Bulan</div>
    <div class="tb-val">10 JT</div>
  </div>
</div>

<!-- PAGES -->
<div class="pages">

  <!-- PAGE: OVERVIEW -->
  <div class="page active" id="page-overview">

    <div class="card">
      <div class="overview-row">
        <div class="donut-wrap">
          <svg viewBox="0 0 110 110">
            <circle cx="55" cy="55" r="42" fill="none" stroke="var(--core)" stroke-width="13" stroke-dasharray="118.7 145.3" stroke-dashoffset="0"/>
            <circle cx="55" cy="55" r="42" fill="none" stroke="var(--agresif)" stroke-width="13" stroke-dasharray="66.0 198.0" stroke-dashoffset="-118.7"/>
            <circle cx="55" cy="55" r="42" fill="none" stroke="var(--growth)" stroke-width="13" stroke-dasharray="52.8 211.2" stroke-dashoffset="-184.7"/>
            <circle cx="55" cy="55" r="42" fill="none" stroke="var(--hedge)" stroke-width="13" stroke-dasharray="26.4 237.6" stroke-dashoffset="-237.5"/>
          </svg>
          <div class="donut-center"><span>Aset</span><span>7 pos</span></div>
        </div>
        <div class="legend">
          <div class="legend-item"><div class="legend-dot-label"><div class="legend-dot" style="background:var(--core)"></div><span class="legend-name">Core Income</span></div><span class="legend-pct">45%</span></div>
          <div class="legend-item"><div class="legend-dot-label"><div class="legend-dot" style="background:var(--agresif)"></div><span class="legend-name">Cashflow Agresif</span></div><span class="legend-pct">25%</span></div>
          <div class="legend-item"><div class="legend-dot-label"><div class="legend-dot" style="background:var(--growth)"></div><span class="legend-name">Growth Global</span></div><span class="legend-pct">20%</span></div>
          <div class="legend-item"><div class="legend-dot-label"><div class="legend-dot" style="background:var(--hedge)"></div><span class="legend-name">Hedge</span></div><span class="legend-pct">10%</span></div>
        </div>
      </div>
    </div>

    <div class="stats-row">
      <div class="stat-card"><div class="sc-label">Eksposur IDX</div><div class="sc-val" style="color:var(--core)">25%</div><div class="sc-sub">DMAS · ADRO · BSSR</div></div>
      <div class="stat-card"><div class="sc-label">Eksposur US</div><div class="sc-val" style="color:var(--growth)">65%</div><div class="sc-sub">ARCC · TCPC · VIG · NEE · GLD</div></div>
      <div class="stat-card"><div class="sc-label">Cadangan Emas</div><div class="sc-val" style="color:var(--hedge)">10%</div><div class="sc-sub">GLD hedge</div></div>
      <div class="stat-card"><div class="sc-label">Posisi Aktif</div><div class="sc-val" style="color:var(--text)">7</div><div class="sc-sub">4 kategori</div></div>
    </div>

  </div>

  <!-- PAGE: HOLDINGS -->
  <div class="page" id="page-holdings">

    <div class="card core">
      <div class="section-hdr"><div class="sh-left"><div class="sh-pip"></div><div class="sh-title">Core Income</div><div class="sh-tag">Cashflow Stabil</div></div><div><div class="sh-pct">45%</div><div class="sh-amount">Rp 4.500.000</div></div></div>
      <div class="holding"><div><div class="hn">Ares Capital (ARCC)</div><div class="ht">BDC · US</div></div><div><div class="ha">Rp 2.000.000</div><div class="hb-row"><div class="hb-bg"><div class="hb" style="width:100%"></div></div><div class="hp">20%</div></div></div></div>
      <div class="holding"><div><div class="hn">Puradelta Lestari (DMAS)</div><div class="ht">Properti · IDX</div></div><div><div class="ha">Rp 1.500.000</div><div class="hb-row"><div class="hb-bg"><div class="hb" style="width:75%"></div></div><div class="hp">15%</div></div></div></div>
      <div class="holding"><div><div class="hn">BlackRock TCP (TCPC)</div><div class="ht">BDC · US</div></div><div><div class="ha">Rp 1.000.000</div><div class="hb-row"><div class="hb-bg"><div class="hb" style="width:50%"></div></div><div class="hp">10%</div></div></div></div>
      <div class="fungsi">⬡ Pondasi utama penghasilan pasif</div>
    </div>

    <div class="card agresif">
      <div class="section-hdr"><div class="sh-le# Ozi-Strategy-
Dana Abadi 
