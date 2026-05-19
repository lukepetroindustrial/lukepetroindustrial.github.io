<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>PPL Calculator — Petro Industrial</title>
<link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@400;600;700;800&family=Barlow:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --orange: #F5820A; --orange-dark: #C96500;
    --bg: #0D0F14; --surface: #161A22; --surface2: #1E242F;
    --border: #2A3040; --text: #EEF0F4; --muted: #7A8494;
    --success: #2ECC71; --success-bg: rgba(46,204,113,0.12);
    --warn: #F39C12; --danger: #E74C3C;
  }
  html, body { background: var(--bg); color: var(--text); font-family: 'Barlow', sans-serif; -webkit-font-smoothing: antialiased; }
  body { display: flex; flex-direction: column; align-items: center; min-height: 100dvh; }

  .header { width: 100%; background: #0a0c10; border-bottom: 2px solid var(--orange); padding: 14px 20px; display: flex; align-items: center; position: sticky; top: 0; z-index: 10; }
  .header h1 { font-family: 'Barlow Condensed', sans-serif; font-size: 22px; font-weight: 800; letter-spacing: 0.05em; text-transform: uppercase; line-height: 1; }
  .header h1 span { color: var(--orange); }
  .header p { font-size: 11px; color: var(--muted); letter-spacing: 0.08em; text-transform: uppercase; margin-top: 3px; }

  .main { width: 100%; max-width: 480px; padding: 24px 16px 48px; display: flex; flex-direction: column; gap: 16px; }

  .section-label { font-family: 'Barlow Condensed', sans-serif; font-size: 11px; font-weight: 700; letter-spacing: 0.12em; text-transform: uppercase; color: var(--orange); margin-bottom: 4px; }

  .field-group { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; overflow: hidden; }
  .field { display: flex; align-items: center; padding: 0 16px; min-height: 64px; gap: 14px; border-bottom: 1px solid var(--border); transition: background 0.15s; }
  .field:last-child { border-bottom: none; }
  .field:focus-within { background: var(--surface2); }

  .field-icon { width: 32px; height: 32px; border-radius: 8px; background: var(--surface2); display: flex; align-items: center; justify-content: center; flex-shrink: 0; border: 1px solid var(--border); }
  .field-icon svg { width: 16px; height: 16px; stroke: var(--orange); fill: none; stroke-width: 2; stroke-linecap: round; stroke-linejoin: round; }

  .field-body { flex: 1; display: flex; flex-direction: column; gap: 2px; }
  .field-label { font-size: 11px; font-weight: 600; letter-spacing: 0.07em; text-transform: uppercase; color: var(--muted); }
  .field-input { background: none; border: none; outline: none; color: var(--text); font-family: 'Barlow Condensed', sans-serif; font-size: 26px; font-weight: 700; width: 100%; line-height: 1; -webkit-appearance: none; appearance: none; }
  .field-input::placeholder { color: var(--border); }
  .field-input.text-input { font-size: 20px; }

  .btn { width: 100%; border: none; border-radius: 12px; font-family: 'Barlow Condensed', sans-serif; font-weight: 800; letter-spacing: 0.08em; text-transform: uppercase; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 10px; -webkit-tap-highlight-color: transparent; touch-action: manipulation; user-select: none; }

  .calc-btn { padding: 18px; background: var(--orange); color: white; font-size: 20px; }
  .calc-btn:active { background: var(--orange-dark); }

  .add-pour-btn { padding: 15px; background: var(--success-bg); border: 1px solid rgba(46,204,113,0.35) !important; color: var(--success); font-size: 18px; }
  .add-pour-btn:active { background: rgba(46,204,113,0.25); }

  .reset-btn { padding: 14px; background: none; border: 1px solid var(--border) !important; color: var(--muted); font-size: 16px; }
  .reset-btn:active { border-color: var(--orange) !important; color: var(--orange); }

  .result-card { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 20px; display: none; animation: slideUp 0.25s ease; }
  .result-card.visible { display: block; }
  .result-card.error { border-color: var(--danger); }
  .result-header { font-size: 11px; font-weight: 600; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); margin-bottom: 6px; }
  .result-value { font-family: 'Barlow Condensed', sans-serif; font-size: 58px; font-weight: 800; color: var(--success); line-height: 1; }
  .result-value.error-text { font-size: 18px; color: var(--danger); font-weight: 600; }

  @keyframes slideUp { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }

  .pour-log-wrap { display: flex; flex-direction: column; gap: 8px; }
  .pour-log-header { display: flex; align-items: center; justify-content: space-between; }
  .clear-log-btn { background: none; border: none; color: var(--muted); font-size: 12px; font-weight: 600; letter-spacing: 0.06em; text-transform: uppercase; cursor: pointer; padding: 4px 0; font-family: 'Barlow', sans-serif; -webkit-tap-highlight-color: transparent; }
  .clear-log-btn:active { color: var(--danger); }

  .pour-record { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 14px 16px; animation: slideUp 0.2s ease; }
  .pour-record-num { font-family: 'Barlow Condensed', sans-serif; font-size: 11px; font-weight: 700; letter-spacing: 0.1em; text-transform: uppercase; color: var(--orange); margin-bottom: 10px; }
  .pour-record-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px 16px; margin-bottom: 10px; }
  .pour-record-label { font-size: 10px; font-weight: 600; letter-spacing: 0.08em; text-transform: uppercase; color: var(--muted); }
  .pour-record-val { font-family: 'Barlow Condensed', sans-serif; font-size: 20px; font-weight: 700; color: var(--text); line-height: 1.1; }
  .pour-record-val.highlight { color: var(--success); }

  .tolerance-row { display: flex; align-items: center; justify-content: space-between; padding-top: 10px; border-top: 1px solid var(--border); flex-wrap: wrap; gap: 6px; }
  .tolerance-label { font-size: 10px; font-weight: 600; letter-spacing: 0.08em; text-transform: uppercase; color: var(--muted); }
  .tolerance-pill { display: inline-flex; align-items: center; gap: 5px; padding: 4px 10px; border-radius: 20px; font-size: 11px; font-weight: 700; letter-spacing: 0.05em; text-transform: uppercase; }
  .tolerance-pill::before { content: '●'; font-size: 8px; }
  .tolerance-pill.good { background: var(--success-bg); color: var(--success); border: 1px solid rgba(46,204,113,0.3); }
  .tolerance-pill.warn { background: rgba(243,156,18,0.12); color: var(--warn); border: 1px solid rgba(243,156,18,0.3); }
  .tolerance-pill.bad  { background: rgba(231,76,60,0.12); color: var(--danger); border: 1px solid rgba(231,76,60,0.3); }
  .no-tank-note { font-size: 10px; color: var(--muted); font-style: italic; padding-top: 10px; border-top: 1px solid var(--border); }

  .footer { text-align: center; font-size: 11px; color: var(--border); letter-spacing: 0.06em; margin-top: 4px; }
</style>
</head>
<body>

<div class="header">
  <div>
    <h1>Petro Industrial — <span>PPL Calculator</span></h1>
    <p>Fluids Management · Field Tool</p>
  </div>
</div>

<div class="main">

  <div>
    <div class="section-label">Job Details</div>
    <div class="field-group">
      <div class="field">
        <div class="field-icon"><svg viewBox="0 0 24 24"><rect x="2" y="7" width="20" height="14" rx="2"/><path d="M16 7V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v2"/></svg></div>
        <div class="field-body">
          <div class="field-label">Job Name</div>
          <input class="field-input text-input" type="text" id="jobName" placeholder="Enter job name">
        </div>
      </div>
      <div class="field">
        <div class="field-icon"><svg viewBox="0 0 24 24"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/></svg></div>
        <div class="field-body">
          <div class="field-label">PIE Number</div>
          <input class="field-input text-input" type="text" id="pieNumber" placeholder="e.g. PIE108085">
        </div>
      </div>
    </div>
  </div>

  <div>
    <div class="section-label">Tank Configuration</div>
    <div class="field-group">
      <div class="field">
        <div class="field-icon"><svg viewBox="0 0 24 24"><ellipse cx="12" cy="5" rx="9" ry="3"/><path d="M3 5v14c0 1.66 4.03 3 9 3s9-1.34 9-3V5"/><path d="M3 12c0 1.66 4.03 3 9 3s9-1.34 9-3"/></svg></div>
        <div class="field-body">
          <div class="field-label">Tank Size (Litres)</div>
          <input class="field-input" type="number" id="tankSize" inputmode="numeric" placeholder="0">
        </div>
      </div>
    </div>
  </div>

  <div>
    <div class="section-label">Meter Inputs</div>
    <div class="field-group">
      <div class="field">
        <div class="field-icon"><svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><path d="M12 8v4l3 3"/></svg></div>
        <div class="field-body">
          <div class="field-label">Current PPL</div>
          <input class="field-input" type="number" id="ppl" inputmode="decimal" placeholder="0.00" step="0.01">
        </div>
      </div>
      <div class="field">
        <div class="field-icon"><svg viewBox="0 0 24 24"><rect x="3" y="3" width="18" height="18" rx="2"/><path d="M9 9h6M9 12h6M9 15h4"/></svg></div>
        <div class="field-body">
          <div class="field-label">Displayed Volume</div>
          <input class="field-input" type="number" id="displayed" inputmode="decimal" placeholder="0.00" step="0.01">
        </div>
      </div>
      <div class="field">
        <div class="field-icon"><svg viewBox="0 0 24 24"><path d="M12 2v20M2 12h20"/><circle cx="12" cy="12" r="4"/></svg></div>
        <div class="field-body">
          <div class="field-label">Dispensed Volume</div>
          <input class="field-input" type="number" id="actual" inputmode="decimal" placeholder="0.00" step="0.01">
        </div>
      </div>
    </div>
  </div>

  <button class="btn calc-btn" id="calcBtn">Calculate New PPL</button>

  <div class="result-card" id="resultCard">
    <div class="result-header">New PPL</div>
    <div class="result-value" id="resultValue"></div>
  </div>

  <button class="btn add-pour-btn" id="addPourBtn" style="display:none">+ Add Pour Record</button>

  <button class="btn reset-btn" id="resetBtn">Clear / Reset All</button>

  <div class="pour-log-wrap" id="pourLogWrap" style="display:none">
    <div class="pour-log-header">
      <div class="section-label" style="margin-bottom:0">Pour Log</div>
      <button class="clear-log-btn" id="clearLogBtn">Clear Log</button>
    </div>
    <div id="pourLog"></div>
  </div>

  <div class="footer">PETRO INDUSTRIAL · FLUIDS MANAGEMENT</div>
</div>

<script>
  var lastResult = null;
  var pourCount  = 0;

  function calculate() {
    var ppl       = parseFloat(document.getElementById('ppl').value);
    var displayed = parseFloat(document.getElementById('displayed').value);
    var actual    = parseFloat(document.getElementById('actual').value);
    var card      = document.getElementById('resultCard');
    var val       = document.getElementById('resultValue');
    var addBtn    = document.getElementById('addPourBtn');

    card.classList.remove('visible','error');
    addBtn.style.display = 'none';
    lastResult = null;

    if (isNaN(ppl) || isNaN(displayed) || isNaN(actual) || actual === 0) {
      val.className = 'result-value error-text';
      val.innerText = 'Please enter all values';
      card.classList.add('visible','error');
      return;
    }

    var newPPL = (displayed / actual) * ppl;
    val.className = 'result-value';
    val.innerText = newPPL.toFixed(2);
    card.classList.add('visible');

    lastResult = { oldPPL: ppl, displayed: displayed, actual: actual, newPPL: newPPL };
    addBtn.style.display = 'flex';
  }

  function getTolerance(displayed, actual, tankSize) {
    if (!tankSize || tankSize <= 0) return null;
    var diff = Math.abs(displayed - actual);
    var pct  = (diff / tankSize) * 100;
    if (pct <= 0.5)  return { cls: 'good', label: pct.toFixed(3) + '% — Within Tolerance' };
    if (pct <= 1.0)  return { cls: 'warn', label: pct.toFixed(3) + '% — Borderline' };
    return             { cls: 'bad',  label: pct.toFixed(3) + '% — Out of Tolerance' };
  }

  function addPourRecord() {
    if (!lastResult) return;

    pourCount++;
    var r         = lastResult;
    var tankSize  = parseFloat(document.getElementById('tankSize').value);
    var tol       = getTolerance(r.displayed, r.actual, tankSize);

    var record = document.createElement('div');
    record.className = 'pour-record';
    record.innerHTML =
      '<div class="pour-record-num">Pour #' + pourCount + '</div>' +
      '<div class="pour-record-grid">' +
        '<div><div class="pour-record-label">Old PPL</div><div class="pour-record-val">' + r.oldPPL.toFixed(2) + '</div></div>' +
        '<div><div class="pour-record-label">New PPL</div><div class="pour-record-val highlight">' + r.newPPL.toFixed(2) + '</div></div>' +
        '<div><div class="pour-record-label">Displayed Vol</div><div class="pour-record-val">' + r.displayed.toFixed(2) + '</div></div>' +
        '<div><div class="pour-record-label">Dispensed Vol</div><div class="pour-record-val">' + r.actual.toFixed(2) + '</div></div>' +
      '</div>' +
      (tol
        ? '<div class="tolerance-row"><div class="tolerance-label">Tolerance (Max 0.5% of tank)</div><span class="tolerance-pill ' + tol.cls + '">' + tol.label + '</span></div>'
        : '<div class="no-tank-note">Enter tank size to calculate tolerance</div>'
      );

    var log = document.getElementById('pourLog');
    log.insertBefore(record, log.firstChild);
    document.getElementById('pourLogWrap').style.display = 'flex';

    // carry forward new PPL, clear volumes
    document.getElementById('ppl').value       = r.newPPL.toFixed(2);
    document.getElementById('displayed').value = '';
    document.getElementById('actual').value    = '';

    document.getElementById('resultCard').classList.remove('visible');
    document.getElementById('addPourBtn').style.display = 'none';
    lastResult = null;

    document.getElementById('displayed').focus();
  }

  function clearLog() {
    pourCount = 0;
    document.getElementById('pourLog').innerHTML = '';
    document.getElementById('pourLogWrap').style.display = 'none';
  }

  function resetAll() {
    ['ppl','displayed','actual','jobName','pieNumber','tankSize'].forEach(function(id) {
      document.getElementById(id).value = '';
    });
    document.getElementById('resultCard').classList.remove('visible','error');
    document.getElementById('resultValue').innerText = '';
    document.getElementById('addPourBtn').style.display = 'none';
    lastResult = null;
    clearLog();
  }

  function bindBtn(id, fn) {
    var el = document.getElementById(id);
    el.addEventListener('click', fn);
    el.addEventListener('touchend', function(e) { e.preventDefault(); fn(); });
  }

  bindBtn('calcBtn', calculate);
  bindBtn('addPourBtn', addPourRecord);
  bindBtn('resetBtn', resetAll);
  bindBtn('clearLogBtn', clearLog);

  document.getElementById('actual').addEventListener('keydown', function(e) { if (e.key === 'Enter') calculate(); });
</script>

</body>
</html>
