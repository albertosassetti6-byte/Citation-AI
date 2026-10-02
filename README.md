Citation AI
Interactive tool that shows how to get cited by major AIs (ChatGPT, Gemini, Claude, Perplexity, Grok, Copilot) through Schema.org structured data, entity strategy and citable content.

https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=fff
https://img.shields.io/badge/CSS3-1572B6?logo=css3&logoColor=fff
https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=000
https://img.shields.io/badge/Chart.js-FF6384?logo=chartdotjs&logoColor=fff

Features
AI Citability Score — gauge with animated percentage per keyword

Live date & time in the top-right corner with green LIVE badge

10 example keyword chips (Bitcoin, Hotel, Sun, Moon, Sea, AI SEO, Coffee, Travel, Fitness, Crypto)

Real data from public APIs:

Wikipedia REST API — entity extract

Wikidata API — entity lookup

Wikimedia Pageviews API — 12-month trend

5 Chart.js visualizations: half-doughnut gauge, bar, line, radar, polar area

Schema.org JSON-LD generator (Article, FAQPage, Organization, Product) with copy button

Interactive SEO checklist that recalculates the score

Fully responsive — mobile, tablet, desktop (hamburger menu on small screens)

Zero dependencies except Chart.js via CDN — no build step

Project structure
text
citation-ai/
├── index.html      # Markup + sections
├── style.css       # White/black theme, responsive layout
└── script.js       # Clock, APIs, charts, generator, analysis
Usage
Clone or download the repo.

Open index.html in a browser — no server required.

Or paste the three files into CodePen (HTML / CSS / JS panels).

Type a keyword or click a chip to run the analysis.

How the score works
baseScore(word) — deterministic hash → 35–80

checklistPercent() — sum of checked items (max 100)

finalScore = base × 0.6 + checklist × 0.4 (capped at 98)

The score is illustrative. Real data (Wikipedia, Wikidata, Pageviews) comes from live public APIs.

APIs used
Service	Endpoint
Wikipedia summary	https://en.wikipedia.org/api/rest_v1/page/summary/{title}
Wikidata search	https://www.wikidata.org/w/api.php?action=wbsearchentities
Wikimedia pageviews	https://wikimedia.org/api/rest_v1/metrics/pageviews/per-article/...
All calls are client-side and CORS-enabled.

License
MIT — free to use, modify and distribute.
