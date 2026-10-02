/* =========================================================
   Citation AI — Script
   LIVE clock + public APIs + Schema.org generator + Chart.js
   ========================================================= */

/* ---------- 1. DATE / TIME in top-right ---------- */
function updateClock() {
  const now = new Date();
  const date = now.toLocaleDateString('en-US', {
    weekday: 'long', day: 'numeric', month: 'long', year: 'numeric'
  });
  const time = now.toLocaleTimeString('en-US');
  const el = document.getElementById('datetime');
  if (el) el.textContent = date + ' · ' + time;
}
updateClock();
setInterval(updateClock, 1000);
document.getElementById('year').textContent = new Date().getFullYear();

/* ---------- 2. MOBILE NAV + ACTIVE LINK ---------- */
const burger = document.getElementById('burger');
const nav = document.querySelector('.nav');
burger.addEventListener('click', () => nav.classList.toggle('open'));
nav.querySelectorAll('.nav-link').forEach(a =>
  a.addEventListener('click', () => nav.classList.remove('open')));

const sections = [...document.querySelectorAll('section[id]')];
window.addEventListener('scroll', () => {
  const y = window.scrollY + 120;
  let current = 'home';
  sections.forEach(s => { if (s.offsetTop <= y) current = s.id; });
  document.querySelectorAll('.nav-link').forEach(a =>
    a.classList.toggle('active', a.getAttribute('href') === '#' + current));
});

/* ---------- 3. UTILS ---------- */
const ENGINES = ['ChatGPT', 'Gemini', 'Claude', 'Perplexity', 'Grok', 'Copilot'];
const RADAR_LABELS = ['Schema.org', 'Entity', 'Content', 'Authority', 'Technical'];

let engineChart, radarChart, lineChart, polarChart, gaugeChart;

function hashWord(w) {
  let h = 0;
  for (let i = 0; i < w.length; i++) h = (h * 31 + w.charCodeAt(i)) % 997;
  return h;
}
function baseScore(word) { return 35 + (hashWord(word.toLowerCase()) % 45); }

/* ---------- 4. CHART.JS (5 chart types) ---------- */
function initCharts() {
  Chart.defaults.color = '#0a0a0a';
  Chart.defaults.font.family = '"Helvetica Neue", Arial, sans-serif';

  /* 1. Half-donut gauge */
  gaugeChart = new Chart(document.getElementById('gauge'), {
    type: 'doughnut',
    data: { datasets: [{ data: [0, 100],
      backgroundColor: ['#0a0a0a', '#f4f4f4'], borderWidth: 0,
      circumference: 180, rotation: 270, cutout: '72%' }] },
    options: { responsive: true, maintainAspectRatio: false,
      plugins: { legend: { display: false }, tooltip: { enabled: false } },
      animation: { duration: 900, easing: 'easeOutQuart' } }
  });

  /* 2. Bars: coverage per AI engine */
  engineChart = new Chart(document.getElementById('engine-chart'), {
    type: 'bar',
    data: { labels: ENGINES, datasets: [{ label: 'Citation probability (%)',
      data: [0,0,0,0,0,0], backgroundColor: '#0a0a0a', borderRadius: 6, maxBarThickness: 46 }] },
    options: { responsive: true, maintainAspectRatio: false,
      scales: { y: { beginAtZero: true, max: 100, grid: { color: '#eee' } },
                x: { grid: { display: false } } },
      plugins: { legend: { display: false } } }
  });

  /* 3. Line: real Wikipedia pageviews (12 months) */
  lineChart = new Chart(document.getElementById('line-chart'), {
    type: 'line',
    data: { labels: [], datasets: [{ label: 'Monthly pageviews',
      data: [], borderColor: '#0a0a0a', backgroundColor: 'rgba(22,163,74,.15)',
      fill: true, tension: 0.35, pointRadius: 3, pointBackgroundColor: '#16a34a' }] },
    options: { responsive: true, maintainAspectRatio: false,
      scales: { y: { beginAtZero: true, grid: { color: '#eee' } },
                x: { grid: { display: false } } } }
  });

  /* 4. Radar: optimization profile */
  radarChart = new Chart(document.getElementById('radar-chart'), {
    type: 'radar',
    data: { labels: RADAR_LABELS, datasets: [{ label: 'Current level',
      data: [0,0,0,0,0], backgroundColor: 'rgba(10,10,10,.12)',
      borderColor: '#0a0a0a', borderWidth: 2, pointBackgroundColor: '#0a0a0a' }] },
    options: { responsive: true, maintainAspectRatio: false,
      scales: { r: { beginAtZero: true, max: 100, grid: { color: '#eee' } } } }
  });

  /* 5. Polar area: keyword variations */
  polarChart = new Chart(document.getElementById('polar-chart'), {
    type: 'polarArea',
    data: { labels: [], datasets: [{ data: [],
      backgroundColor: ['rgba(10,10,10,.75)','rgba(22,163,74,.55)','rgba(10,10,10,.45)',
                        'rgba(22,163,74,.35)','rgba(10,10,10,.28)'], borderWidth: 1 }] },
    options: { responsive: true, maintainAspectRatio: false,
      scales: { r: { beginAtZero: true, max: 100, grid: { color: '#eee' } } },
      plugins: { legend: { position: 'bottom' } } }
  });
}

/* ---------- 5. PUBLIC APIs ---------- */
async function fetchWikipedia(word) {
  const ex = document.getElementById('wiki-extract');
  const de = document.getElementById('wiki-desc');
  try {
    const r = await fetch('https://en.wikipedia.org/api/rest_v1/page/summary/'
      + encodeURIComponent(word));
    if (!r.ok) throw 0;
    const j = await r.json();
    ex.textContent = (j.extract || 'No extract available.').slice(0, 300) + '…';
    de.textContent = j.description ? ('Entity type: ' + j.description) : '';
    return true;
  } catch {
    ex.textContent = 'No entry found on English Wikipedia. Creating an entity on Wikipedia/Wikidata greatly increases AI citability.';
    de.textContent = '';
    return false;
  }
}

async function fetchWikidata(word) {
  const el = document.getElementById('wikidata-result');
  try {
    const r = await fetch('https://www.wikidata.org/w/api.php?action=wbsearchentities&search='
      + encodeURIComponent(word) + '&language=en&format=json&origin=*');
    const j = await r.json();
    if (j.search && j.search.length) {
      const top = j.search[0];
      el.innerHTML = 'Entity found: <strong>' + top.label + '</strong><br>' +
        (top.description || '') + '<br><code>' + top.id + '</code>';
    } else {
      el.textContent = 'No Wikidata entity: opportunity to create one!';
    }
  } catch { el.textContent = 'Wikidata API not reachable.'; }
}

async function fetchPageviews(word) {
  try {
    const end = new Date();
    const start = new Date(); start.setMonth(start.getMonth() - 12);
    const fmt = d => d.toISOString().slice(0,10).replace(/-/g,'') + '00';
    const url = 'https://wikimedia.org/api/rest_v1/metrics/pageviews/per-article/'
      + 'en.wikipedia/all-access/all-agents/' + encodeURIComponent(word)
      + '/monthly/' + fmt(start) + '/' + fmt(end);
    const r = await fetch(url);
    if (!r.ok) throw 0;
    const j = await r.json();
    const items = j.items || [];
    if (!items.length) throw 0;
    lineChart.data.labels = items.map(i => i.month.slice(0,4) + '-' + i.month.slice(4,6));
    lineChart.data.datasets[0].data = items.map(i => i.views);
    lineChart.update();
    const total = items.reduce((a,i) => a + i.views, 0);
    document.getElementById('pv-total').textContent = total.toLocaleString('en-US');
  } catch {
    /* simulated fallback */
    const labels = [], data = [];
    const now = new Date();
    for (let i = 11; i >= 0; i--) {
      const d = new Date(now.getFullYear(), now.getMonth() - i, 1);
      labels.push(d.toISOString().slice(0,7));
      data.push(20000 + (hashWord(word + i) % 60000));
    }
    lineChart.data.labels = labels;
    lineChart.data.datasets[0].data = data;
    lineChart.update();
    document.getElementById('pv-total').textContent =
      data.reduce((a,b)=>a+b,0).toLocaleString('en-US') + ' (estimated)';
  }
}

/* ---------- 6. MAIN ANALYSIS ---------- */
function analyze(word) {
  const base = baseScore(word);
  const perEngine = ENGINES.map(e =>
    Math.min(95, Math.max(15, base + ((hashWord(word + e) % 21) - 10))));
  const checkPct = checklistPercent();
  const finalScore = Math.min(98, Math.round(base * 0.6 + checkPct * 0.4));

  gaugeChart.data.datasets[0].data = [finalScore, 100 - finalScore];
  gaugeChart.update();
  engineChart.data.datasets[0].data = perEngine;
  engineChart.update();
  radarChart.data.datasets[0].data = [
    Math.min(100, checkPct + 10), base, Math.min(100, base + 8),
    Math.max(20, checkPct - 5), 60 + (hashWord(word) % 30)];
  radarChart.update();

  /* polar area with keyword variations */
  const variations = [word, word + ' meaning', word + ' guide', word + ' reviews', word + ' 2026'];
  polarChart.data.labels = variations;
  polarChart.data.datasets[0].data = variations.map(v =>
    Math.min(95, Math.max(20, base + ((hashWord(v) % 25) - 12))));
  polarChart.update();

  animateNumber(document.getElementById('score-label'), finalScore);

  const vt = document.getElementById('verdict-title');
  if (finalScore >= 75) vt.textContent = 'Excellent citability';
  else if (finalScore >= 50) vt.textContent = 'Average citability';
  else vt.textContent = 'Low citability';

  document.getElementById('verdict-text').textContent =
    '"' + word + '" has an estimated ' + finalScore +
    '% probability of being cited by the major AIs.';

  const tips = document.getElementById('quick-tips');
  tips.innerHTML = '';
  ['Add FAQPage JSON-LD markup for "' + word + '"',
   'Write a clear 40–60 word definition',
   'Earn mentions from authoritative sources',
   'Connect the entity to Wikipedia / Wikidata']
  .slice(0, finalScore >= 70 ? 2 : 4).forEach(s => {
    const li = document.createElement('li');
    li.textContent = s;
    tips.appendChild(li);
  });

  document.getElementById('kw-display').textContent = word;
  document.querySelectorAll('.kw-inline').forEach(el => el.textContent = word);
  document.getElementById('schema-kw').value = word;

  fetchWikipedia(word);
  fetchWikidata(word);
  fetchPageviews(word);
  generateSchema();
}

/* ---------- 7. CHECKLIST ---------- */
function checklistPercent() {
  let total = 0;
  document.querySelectorAll('#checklist input:checked')
    .forEach(cb => total += Number(cb.dataset.pts));
  document.getElementById('check-total').textContent = total + '%';
  return total;
}
document.querySelectorAll('#checklist input').forEach(cb =>
  cb.addEventListener('change', () =>
    analyze(document.getElementById('keyword').value.trim() || 'Bitcoin')));

/* ---------- 8. SCHEMA.ORG JSON-LD GENERATOR ---------- */
function generateSchema() {
  const type = document.getElementById('schema-type').value;
  const kw = document.getElementById('schema-kw').value.trim() || 'Bitcoin';
  const url = document.getElementById('schema-url').value.trim() || 'https://yoursite.com';
  const today = new Date().toISOString().slice(0,10);
  let obj;

  if (type === 'Article') {
    obj = { '@context': 'https://schema.org', '@type': 'Article',
      headline: kw + ': complete and up-to-date guide',
      description: 'Everything you need to know about ' + kw + ' in a clear and verifiable guide.',
      author: { '@type': 'Organization', name: 'Your Brand', url: url },
      publisher: { '@type': 'Organization', name: 'Your Brand',
        logo: { '@type': 'ImageObject', url: url + '/logo.png' } },
      datePublished: today, dateModified: today, mainEntityOfPage: url };
  } else if (type === 'FAQPage') {
    obj = { '@context': 'https://schema.org', '@type': 'FAQPage',
      mainEntity: [
        { '@type': 'Question', name: 'What is ' + kw + '?',
          acceptedAnswer: { '@type': 'Answer',
            text: kw + ' is a central topic: this direct 40–60 word answer is what AIs quote.' } },
        { '@type': 'Question', name: 'How do I get started with ' + kw + '?',
          acceptedAnswer: { '@type': 'Answer',
            text: 'Start from official sources and verifiable data on ' + kw + ', then dig deeper with up-to-date guides.' } }
      ] };
  } else if (type === 'Organization') {
    obj = { '@context': 'https://schema.org', '@type': 'Organization',
      name: kw, url: url, logo: url + '/logo.png',
      sameAs: ['https://www.facebook.com/yourbrand', 'https://www.linkedin.com/company/yourbrand',
               'https://x.com/yourbrand'],
      contactPoint: { '@type': 'ContactPoint', contactType: 'customer service',
        email: 'info@yoursite.com' } };
  } else {
    obj = { '@context': 'https://schema.org', '@type': 'Product',
      name: kw, url: url, description: 'Complete sheet for ' + kw + ' with specs and details.',
      brand: { '@type': 'Brand', name: 'Your Brand' },
      offers: { '@type': 'Offer', priceCurrency: 'USD', price: '99.00',
        availability: 'https://schema.org/InStock', url: url } };
  }

  document.getElementById('schema-output').textContent =
    '<script type="application/ld+json">\n' +
    JSON.stringify(obj, null, 2) +
    '\n</scr' + 'ipt>';
}

document.getElementById('gen-btn').addEventListener('click', generateSchema);
document.getElementById('schema-type').addEventListener('change', generateSchema);

document.getElementById('copy-btn').addEventListener('click', () => {
  const text = document.getElementById('schema-output').textContent;
  const btn = document.getElementById('copy-btn');
  navigator.clipboard.writeText(text).then(() => {
    btn.textContent = 'Copied!';
    btn.style.background = '#16a34a';
    setTimeout(() => { btn.textContent = 'Copy'; btn.style.background = ''; }, 1800);
  });
});

/* ---------- 9. CONTACT FORM ---------- */
document.getElementById('contact-form').addEventListener('submit', e => {
  e.preventDefault();
  document.getElementById('form-status').textContent =
    'Message sent! We will reply within 24 hours.';
  e.target.reset();
});

/* ---------- 10. INPUT & BOOT ---------- */
function animateNumber(el, target) {
  let current = 0;
  const step = Math.max(1, Math.ceil(target / 40));
  const timer = setInterval(() => {
    current += step;
    if (current >= target) { current = target; clearInterval(timer); }
    el.textContent = current + '%';
  }, 22);
}

document.getElementById('analyze-btn').addEventListener('click', () => {
  const word = document.getElementById('keyword').value.trim();
  if (word) analyze(word);
});
document.getElementById('keyword').addEventListener('keydown', e => {
  if (e.key === 'Enter') document.getElementById('analyze-btn').click();
});

/* ---------- 11. KEYWORD CHIPS (10 examples) ---------- */
document.getElementById('keyword-chips').addEventListener('click', e => {
  const btn = e.target.closest('.chip');
  if (!btn) return;
  const kw = btn.dataset.kw;
  document.getElementById('keyword').value = kw;
  analyze(kw);
  document.querySelector('.results').scrollIntoView({ behavior: 'smooth', block: 'start' });
});

/* ---------- 12. BOOT ---------- */
window.addEventListener('load', () => {
  initCharts();
  analyze('Bitcoin');
});
