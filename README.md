# Readiness-framework 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Project Readiness Framework</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif; background: #f4f6f9; color: #1a1a1a; min-height: 100vh; padding: 0; }

  .top-banner { background: #003087; padding: 20px 32px 16px; border-bottom: 4px solid #CC0000; }
  .top-banner h1 { font-size: 22px; font-weight: 600; color: #fff; letter-spacing: 0.02em; }
  .top-banner p { font-size: 13px; color: #a8bdd4; margin-top: 4px; }

  .content { padding: 24px 32px; }

  .dim-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; margin-bottom: 20px; }

  .dim-card { background: #fff; border-radius: 4px; overflow: hidden; border: 1px solid #dde3ec; }
  .dim-header { background: #003087; padding: 12px 16px 10px; border-bottom: 3px solid #CC0000; }
  .dim-label { font-size: 10px; font-weight: 600; letter-spacing: 0.08em; text-transform: uppercase; color: #a8bdd4; margin-bottom: 2px; }
  .dim-title { font-size: 16px; font-weight: 600; color: #fff; }
  .dim-desc { font-size: 11px; color: #a8bdd4; margin-top: 4px; line-height: 1.5; }

  .q-list { padding: 4px 0; }
  .q-row { display: flex; align-items: flex-start; gap: 12px; padding: 10px 16px; border-bottom: 1px solid #f0f3f7; cursor: pointer; transition: background 0.15s; }
  .q-row:last-child { border-bottom: none; }
  .q-row:hover { background: #f4f6f9; }
  .q-text { font-size: 13px; color: #2c3e50; line-height: 1.5; flex: 1; padding-top: 1px; }

  .toggle { width: 40px; height: 22px; border-radius: 2px; border: 1.5px solid #ccd3de; background: #e8ecf2; flex-shrink: 0; position: relative; transition: background 0.2s, border-color 0.2s; }
  .toggle.on { background: #CC0000; border-color: #990000; }
  .toggle-knob { position: absolute; top: 2px; left: 2px; width: 14px; height: 14px; border-radius: 1px; background: #fff; box-shadow: 0 1px 3px rgba(0,0,0,0.25); transition: transform 0.2s; }
  .toggle.on .toggle-knob { transform: translateX(18px); }

  .summary-bar { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; margin-bottom: 16px; }
  .score-card { background: #fff; border: 1px solid #dde3ec; border-top: 3px solid #003087; border-radius: 4px; padding: 14px 16px; text-align: center; }
  .score-label { font-size: 11px; color: #6b7a8d; margin-bottom: 6px; font-weight: 500; text-transform: uppercase; letter-spacing: 0.04em; }
  .score-val { font-size: 24px; font-weight: 600; }
  .score-sub { font-size: 11px; color: #9aaab8; margin-top: 3px; }
  .progress-track { margin: 8px 0 4px; background: #e8ecf2; border-radius: 2px; height: 6px; overflow: hidden; }
  .progress-fill { height: 100%; border-radius: 2px; transition: width 0.35s ease; }

  .verdict { background: #003087; border-radius: 4px; padding: 16px 20px; display: flex; align-items: center; justify-content: space-between; gap: 16px; border-left: 6px solid #CC0000; }
  .verdict-title { font-size: 15px; font-weight: 600; color: #fff; }
  .verdict-sub { font-size: 12px; color: #a8bdd4; margin-top: 3px; }
  .badge { font-size: 12px; font-weight: 600; padding: 6px 16px; border-radius: 2px; white-space: nowrap; letter-spacing: 0.03em; }

  .chevron-divider { display: flex; align-items: center; margin-bottom: 20px; gap: 0; }
  .chev { width: 0; height: 0; }

  @media (max-width: 700px) {
    .dim-grid, .summary-bar { grid-template-columns: 1fr; }
    .content { padding: 16px; }
    .top-banner { padding: 16px; }
  }
</style>
</head>
<body>

<div class="top-banner">
  <h1>Project Readiness Framework</h1>
  <p>FPSO Production Data Web App &amp; Reporting Dashboard — toggle each criterion as confirmed</p>
</div>

<div class="content">
  <div class="dim-grid" id="dims"></div>
  <div class="summary-bar" id="summary"></div>
  <div class="verdict" id="verdict"></div>
</div>

<script>
const dims = [
  {
    id: 'feasible',
    title: 'Feasibility',
    label: 'Can we build it?',
    desc: 'Is the solution technically achievable within available constraints?',
    questions: [
      'Is the scope clearly defined and bounded?',
      'Is the technology stack proven and available?',
      'Is the need still present and a priority?'
    ]
  },
  {
    id: 'ready',
    title: 'Readiness',
    label: 'Are we prepared?',
    desc: 'Do we have the people, ownership and infrastructure to deliver?',
    questions: [
      'Team and technical resources available?',
      'Stakeholders aligned and engaged?',
      'Are the users prepared to interact with GenAI outputs?'
    ]
  },
  {
    id: 'value',
    title: 'Value',
    label: 'Is it worth doing?',
    desc: 'Is there clear, agreed business value with measurable outcomes?',
    questions: [
      'Potential value agreed by all stakeholders?',
      'Defined metrics exist to demonstrate value?',
      'Value achievable within project timeframe?'
    ]
  }
];

const state = {};
dims.forEach(d => { state[d.id] = d.questions.map(() => false); });

function toggle(dimId, idx) {
  state[dimId][idx] = !state[dimId][idx];
  render();
}

function render() {
  renderDims();
  renderSummary();
}

function renderDims() {
  const container = document.getElementById('dims');
  container.innerHTML = '';
  dims.forEach(d => {
    const card = document.createElement('div');
    card.className = 'dim-card';
    card.innerHTML = `
      <div class="dim-header">
        <div class="dim-label">${d.label}</div>
        <div class="dim-title">${d.title}</div>
        <div class="dim-desc">${d.desc}</div>
      </div>
      <div class="q-list">
        ${d.questions.map((q, i) => `
          <div class="q-row" onclick="toggle('${d.id}',${i})">
            <div class="q-text">${q}</div>
            <div class="toggle ${state[d.id][i] ? 'on' : ''}">
              <div class="toggle-knob"></div>
            </div>
          </div>
        `).join('')}
      </div>
    `;
    container.appendChild(card);
  });
}

function renderSummary() {
  const summaryEl = document.getElementById('summary');
  summaryEl.innerHTML = '';
  let total = 0, totalMax = 0;

  dims.forEach(d => {
    const score = state[d.id].filter(Boolean).length;
    const max = d.questions.length;
    total += score; totalMax += max;
    const pct = Math.round((score / max) * 100);
    const fillColor = pct === 100 ? '#CC0000' : pct >= 50 ? '#003087' : '#9aaab8';
    const valColor = pct === 100 ? '#CC0000' : pct >= 50 ? '#003087' : '#9aaab8';
    const card = document.createElement('div');
    card.className = 'score-card';
    card.innerHTML = `
      <div class="score-label">${d.title}</div>
      <div class="score-val" style="color:${valColor}">${score}/${max}</div>
      <div class="progress-track">
        <div class="progress-fill" style="width:${pct}%; background:${fillColor}"></div>
      </div>
      <div class="score-sub">${pct}% confirmed</div>
    `;
    summaryEl.appendChild(card);
  });

  const overall = Math.round((total / totalMax) * 100);
  const verdictEl = document.getElementById('verdict');
  let badgeColor, badgeBg, status, guidance;
  if (overall >= 80) {
    badgeColor = '#CC0000'; badgeBg = '#fff'; status = 'Ready to proceed';
    guidance = 'All key criteria confirmed. Project can move to planning and execution.';
  } else if (overall >= 50) {
    badgeColor = '#003087'; badgeBg = '#fff'; status = 'Gaps to resolve';
    guidance = 'Some criteria unconfirmed. Address open items before committing to full delivery.';
  } else {
    badgeColor = '#fff'; badgeBg = '#CC0000'; status = 'Not ready';
    guidance = 'Significant gaps remain. Revisit scope, ownership, or value case before proceeding.';
  }
  verdictEl.innerHTML = `
    <div>
      <div class="verdict-title">Overall readiness — ${overall}%</div>
      <div class="verdict-sub">${guidance}</div>
    </div>
    <span class="badge" style="background:${badgeBg}; color:${badgeColor}">${status}</span>
  `;
}

render();
</script>
</body>
</html>
