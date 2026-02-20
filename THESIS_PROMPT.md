# Thesis HTML Generator — Prompt Template

Use this prompt with Claude Code to generate a styled, self-contained HTML thesis document from your thesis notes.

---

## Prerequisites

1. Your thesis content should already exist in `public/data/thesis-notes.json` (or you can provide raw notes to be transformed)
2. Reference the design template: `public/thesis/private/bitcoin-pot5-thesis.html`
3. Reference the One Peak style origin: `/Users/krisrajah/Desktop/TradFi Crypto Headcount Analysis.html`

---

## The Prompt

```
Based on the design style of public/thesis/private/bitcoin-pot5-thesis.html, create a new thesis HTML document for [THESIS NAME FROM thesis-notes.json].

Requirements:

1. STRUCTURE: Identify 4-6 logical sections/chapters from the thesis content, plus a clear Conclusion & Recommendation section at the end. Each section should have:
   - A numbered label (01 / Section Name)
   - A bold headline
   - A subtitle/description paragraph
   - Appropriate data visualisation (cards, bar charts, comparison grids, tables)

2. STYLE: Match the existing template exactly:
   - Tailwind CSS via CDN with Space Grotesk / IBM Plex Mono / Inter fonts
   - Sand/beige/charcoal colour palette with accent colour (adapt accent to suit the asset — e.g. th-btc orange for Bitcoin, could use different accent for other assets)
   - Glass-card components, mono-label uppercase section markers
   - Material Symbols Outlined icons
   - Sticky header nav with section links
   - Hero section with thesis title and one-line summary

3. KEY METRICS STRIP: Below the hero, include 4 key metrics cards (the most important numbers from the thesis). Include "As of [thesis date]" on the primary metric.

4. SCENARIO CARDS: If the thesis has bull/base/bear scenarios, display them as equal-sized cards with probability clearly labelled as "XX% Probability", price targets as the big number, and IRR below.

5. DATA SOURCES: Include a "Methodology & Citations" section before the footer with numbered sources (01-06) in the mono-label uppercase style, referencing actual sources from the thesis content.

6. FOOTER: Kris Rajah branding, copyright 2026, "Not Financial Advice" label.

7. TWO VERSIONS (sequential — do NOT run in parallel):
   - FIRST: Generate thesis/private/[slug].html — full version including a "Portfolio Framework" section showing how this fits the five-pot system (pot allocation, rules, why it belongs in its specific pot)
   - THEN: Copy the private version to thesis/public/[slug].html and remove: the Portfolio Framework section, its nav link, adjust section numbers, and strip any references to pots/ISA structure in hero/header/footer

8. CONTENT: Stick to what I've written 90% of the time. Only do additional research if there are gaps. Do not invent data or statistics. If the thesis-notes.json entry lacks sufficient detail or sources for the citations section, ask the user for a raw research dump before generating.

9. RESPONSIVENESS: Ensure no text overflow on cards — prefer stacked layouts (number on top, label below) over side-by-side layouts for metric displays inside narrow cards.

10. PRICE CHART: Include a Chart.js "Growth of $100" comparison chart in the Share Price / Performance section. This shows how the asset compares to peers and benchmarks over time, perfectly styled to match the sand/mono design system. Requires hardcoded data — ask user for calendar year returns or research them to compute cumulative growth from $100.

    Add `<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.7/dist/chart.umd.min.js"></script>` to `<head>`.

    HTML (in the Share Price section):
    ```html
    <div class="glass-card p-10 rounded-3xl">
        <h3 class="mono-label text-th-charcoal/40 mb-2">Growth of $100 Invested (Jan YYYY → Present)</h3>
        <p class="text-sm text-th-charcoal/50 font-light mb-6">Normalised total return comparison vs peers and benchmarks.</p>
        <div class="relative" style="height: 380px;">
            <canvas id="growthChart"></canvas>
        </div>
        <div class="flex justify-center gap-8 mt-6">
            <div class="flex items-center gap-2"><span class="w-4 h-1 rounded-full bg-[#ACCENT] block"></span><span class="mono-label text-[10px] text-th-charcoal/50">ASSET</span></div>
            <div class="flex items-center gap-2"><span class="w-4 h-1 rounded-full bg-[#e67e5f] block"></span><span class="mono-label text-[10px] text-th-charcoal/50">PEER</span></div>
            <div class="flex items-center gap-2"><span class="w-4 h-1 rounded-full bg-[#b7c4b6] block"></span><span class="mono-label text-[10px] text-th-charcoal/50">SPY</span></div>
        </div>
    </div>
    ```

    Chart.js config (place in `<script>` before `</body>`):
    ```javascript
    const ctx = document.getElementById('growthChart').getContext('2d');
    new Chart(ctx, {
        type: 'line',
        data: {
            labels: ['Jan 16','Jan 17', /* ... */ 'Feb 26'],
            datasets: [
                {
                    label: 'ASSET',
                    data: [100, /* cumulative growth values from calendar year returns */],
                    borderColor: '#ACCENT',           // use thesis accent colour
                    backgroundColor: 'rgba(R,G,B,0.08)', // accent at 8% opacity
                    fill: true, tension: 0.3, borderWidth: 2.5,
                    pointRadius: 0, pointHoverRadius: 5
                },
                {
                    label: 'PEER',
                    data: [100, /* ... */],
                    borderColor: '#e67e5f',
                    backgroundColor: 'transparent',
                    fill: false, tension: 0.3, borderWidth: 2,
                    borderDash: [6, 3], pointRadius: 0, pointHoverRadius: 5
                },
                {
                    label: 'SPY',
                    data: [100, /* ... */],
                    borderColor: '#b7c4b6',
                    backgroundColor: 'transparent',
                    fill: false, tension: 0.3, borderWidth: 1.5,
                    borderDash: [3, 3], pointRadius: 0, pointHoverRadius: 5
                }
            ]
        },
        options: {
            responsive: true, maintainAspectRatio: false,
            interaction: { mode: 'index', intersect: false },
            plugins: {
                legend: { display: false },
                tooltip: {
                    backgroundColor: '#1a1a1a',
                    titleFont: { family: 'IBM Plex Mono', size: 10 },
                    bodyFont: { family: 'Inter', size: 12 },
                    padding: 12, cornerRadius: 8,
                    callbacks: { label: ctx => ctx.dataset.label + ': $' + ctx.parsed.y.toFixed(0) }
                }
            },
            scales: {
                x: { grid: { display: false }, ticks: { font: { family: 'IBM Plex Mono', size: 9 }, color: 'rgba(26,26,26,0.3)', maxRotation: 45 } },
                y: { grid: { color: 'rgba(26,26,26,0.05)' }, ticks: { font: { family: 'IBM Plex Mono', size: 9 }, color: 'rgba(26,26,26,0.3)', callback: v => '$' + v } }
            }
        }
    });
    ```

    **Peer selection guide:** Pick 1-2 direct peers + SPY as benchmark. E.g., ASML vs AMAT vs SPY, IGV vs QQQ vs SPY, BTC vs ETH vs SPY. Primary asset gets the accent colour with fill; peers get dashed lines in terracotta and sage.
```

---

## Section Structure Guide

Different thesis types map to different natural sections:

### For an Equity / Fund Thesis (e.g. ASML, Jupiter India, Yellow Cake)
1. Executive Summary / The Opportunity
2. Company/Fund Analysis (fundamentals, track record, holdings)
3. Macro / Sector Thesis (industry drivers, tailwinds)
4. Risk Assessment (valuation, concentration, macro risks)
5. Portfolio Framework (private only — pot fit, sizing)
6. Conclusion & Recommendation (buy/hold/avoid, entry strategy, exit triggers)

### For a Crypto / Macro Thesis (e.g. Bitcoin)
1. The Crash / Event Anatomy (what happened, timeline)
2. On-Chain / Technical Signals (data-driven indicators)
3. Historical Context (cycle comparisons, precedents)
4. Bull vs Bear Case (side-by-side analysis with scenarios)
5. Portfolio Framework (private only — pot fit, rules)
6. Conclusion & Recommendation (entry strategy, position sizing, exit triggers)

### For an ETF / Diversification Thesis (e.g. IGV)
1. ETF Deconstruction (holdings, overlap analysis, fees)
2. Thesis Stress Test (bull/bear for underlying sector)
3. Correlation & Portfolio Fit (vs existing holdings)
4. Comparative Analysis (vs alternative vehicles)
5. Portfolio Framework (private only — pot fit)
6. Conclusion & Recommendation (buy/wait/avoid framework)

---

## Colour Accent Guide

Adapt the accent colour to the asset:
- **Bitcoin/Crypto:** `#f7931a` (Bitcoin orange)
- **Semiconductor/Tech:** `#6366f1` (indigo)
- **India/EM:** `#10b981` (emerald)
- **Uranium/Energy:** `#eab308` (amber/yellow)
- **Software/SaaS:** `#8b5cf6` (violet)
- **Default/General:** `#e67e5f` (the original One Peak terracotta)

---

## File Naming Convention

Use the thesis note ID as the slug:
- `thesis/private/2026-02-17-bitcoin-pot5-thesis.html`
- `thesis/public/2026-02-17-bitcoin-pot5-thesis.html`

---

## Quick Start

```
Read public/data/thesis-notes.json and find the [THESIS NAME] entry.
Then read public/thesis/private/bitcoin-pot5-thesis.html as the design reference.
Generate the new thesis following the requirements above.
```
