# Project Nexus

**Measurement Dashboard** for a demand generation program.

Single-file HTML dashboard hosted on GitHub Pages. No build step, no dependencies, no server. Update the JSON data block, push to main, and the live dashboard reflects the changes.

## Live URL

`https://nexusreporting.github.io/Nexus/`

Password protected. Contact the project lead for access.

---

## What it does

Tracks the full lifecycle of a demand generation pilot: lead sourcing through content syndication, marketing automation and qualification, AI outbound calling, and partner opportunity delivery. Designed for a mixed audience of program operators, functional leadership, and external stakeholders.

**11 tabs:**

- **Summary** - KPI tiles, weekly volume trend, pickup rate, open measurement gaps
- **Targets and Pace** - Quarterly PO burndown, velocity tracking, pipeline feed vs target, revenue model
- **Funnel and Age** - Lifecycle stage counts, stage dwell time, attrition rates, qualification gates
- **AI Calls (NLPearl)** - Attempt and connect volume, pickup rate, call signal distribution, SLA compliance, benchmarks
- **Campaign Reporting** - Audience intelligence: job function, revenue size, employee count, industry, VM environment, migration timeline, decision role, geographic distribution, campaign pacing
- **Email** - Consent and link testing status, benchmarks
- **Web** - Analytics tagging status, implementation checklist, benchmarks
- **Improvement Plan** - Governance cadence and CIP structure
- **Lead Scoring** - Fit vs activity scoring matrix, weight configuration, decay rules
- **Metric Register** - Full metric catalog with MET IDs, calculations, sources, and priority tiers
- **Glossary** - Searchable reference for all program terms, acronyms, and systems

---

## Features

- **Password gate** - Prompt on page load. Session-persisted so refresh does not re-prompt.
- **Light and dark theme** - Toggle in the topbar. Defaults to light.
- **Edit mode** - Inline field editing with changelog tracking. Save and Export downloads a new versioned HTML file with updated data baked in.
- **Collapsible insight notes** - 23 analyst notes throughout the dashboard, collapsed by default. Hidden on mobile.
- **Chart tooltips** - Hover any canvas chart to see exact data point values.
- **Print support** - Print Tab button in the footer. Sidebar hidden, active tab expands to full width. Notes auto-expand in print.
- **Mobile responsive** - Hamburger nav, single-column reflow, adaptive typography below 768px.
- **Persistent data footer** - Fixed bar showing data-as-of date, version number, and print button.
- **Search** - Topbar search filters sidebar nav by keyword across all tabs.
- **Glossary search** - Dedicated search within the glossary tab.

---

## How to update the dashboard

All data lives in a single JSON block at the top of `index.html`. You do not need to be a developer to update it. If you can open a file in a text editor and change a number, you can update the dashboard.

### What you need

- A plain text editor. In order of preference:
  - **VS Code** (free at code.visualstudio.com) - recommended
  - **Notepad** (Windows, built-in) - works fine
  - **TextEdit** (Mac, built-in) - must be in plain text mode
- Do not use Microsoft Word. It will corrupt the file.

### Step-by-step

**1. Download the file**

Go to the repository on GitHub, click `index.html`, then click the download icon (or use `Code > Download ZIP`). Save it to your desktop.

**2. Open in a text editor**

Open the file in VS Code or Notepad. Do not open it in Word or any rich-text editor.

**3. Find the data block**

At the very top of the file, search for:

```
NEXUS DASHBOARD - DATA BLOCK
```

Use `Ctrl+F` (Windows) or `Cmd+F` (Mac) to jump straight to it. This is the only section you will edit. Everything below it is layout and logic - do not touch it.

**4. Update the values**

Each field has a comment explaining what it is and where it appears. For example:

```json
"q1_delivered": 0,          ← update when POs are confirmed in ACE
"pickup_rate_pct": 24,      ← from the weekly NLPearl export
"total_in_funnel": 264,     ← from Eloqua pipeline pull
```

Change only the value after the colon. Do not change the field name, the quotes, or the comma at the end of the line.

**5. Save the file**

`Ctrl+S` on Windows, `Cmd+S` on Mac.

**6. Upload to GitHub**

Go to the repository, click `Add file > Upload files`, drag in your updated `index.html`, and commit directly to `main`. GitHub Pages redeploys automatically, usually within 2 minutes.

**7. Verify**

Open `https://nexusreporting.github.io/Nexus/` and confirm the numbers look correct before sharing.

---

### Alternatively: use Edit Mode

If you prefer not to touch the file directly, use Edit Mode in the live dashboard:

1. Open the dashboard and enter the password
2. Click **Edit Mode** in the topbar
3. Click any data field to edit it inline
4. When done, click **Save and Export** - this downloads a new versioned HTML file with all changes baked in
5. Upload that file to GitHub as `index.html`

---

### Which fields to update and who owns them

| Field | When |
|---|---|
| Funnel counts (Known, Validation, MEL, PO, Recycled, DQ) | Weekly, after Eloqua pipeline pull |
| PO delivered (`q1_delivered`, `total_delivered`) | As soon as ACE uploads are confirmed |
| Weeks remaining (`q1_weeks_remaining`) | Weekly |
| NLPearl call stats (attempts, pickup rate, signals) | Weekly, from NLPearl export |
| Trend chart arrays (`mel`, `mqp`, `po`, `rates`) | Weekly - append one value per array |
| Campaign totals (`total_leads`, `spend_to_date`) | When new batch arrives |
| Open gaps text (`open_gaps_text`) | When open items change status |
| Summary hero subtitle | Weekly or when key numbers shift |
| Report date and as-of label | Each update cycle |

Fields not listed (audience intelligence tables, geographic data, industry breakdowns) are updated only when a new content syndication batch arrives. Those require a full batch regeneration rather than a manual edit.

---

### Common mistakes to avoid

**Editing the wrong part of the file**
The data block is at the very top. If you are scrolling through hundreds of lines of code, you have gone too far. Use `Ctrl+F` to search for `NEXUS DASHBOARD - DATA BLOCK`.

**Breaking the JSON format**
Each line follows this pattern:
```
"field_name": value,
```
Common errors that will break the dashboard:
- Deleting a comma at the end of a line
- Deleting a quotation mark around a text value
- Changing a field name instead of its value
- Using a comma inside a number (write `79900`, not `79,900`)

If the dashboard opens as a blank page or shows no data after uploading, the JSON is likely broken. Re-download the previous version from the repository and start over from there.

**Opening the file in Word**
Word will corrupt the HTML. Always use a plain text editor. If you accidentally open in Word, close without saving and use the GitHub copy.

---

## Developer reference: JSON data block

The entire dashboard state is driven by a single JSON object embedded in `index.html` inside a `<script id="nexus-data" type="application/json">` tag. The dashboard reads this on load and renders all values from it. Nothing is hardcoded in the HTML.

### Finding it

Search the file for:
```
NEXUS DASHBOARD - DATA BLOCK
```

The block starts immediately after that comment and ends before the closing `</script>` tag. Everything between those points is the data object.

### Structure overview

```
{
  "meta"          - version, report date, revenue assumption
  "funnel"        - Eloqua stage counts and dwell times
  "targets"       - quarterly PO targets and delivered counts
  "calls"         - NLPearl attempt/connect/signal data
  "campaign"      - content syndication batch totals and offer breakdown
  "gaps"          - free-text fields for hero subtitles and open items
  "chart_trend"   - weekly MEL/MQP/PO arrays for the trend chart
  "chart_pickup"  - weekly pickup rate array
  "chart_age"     - stage average dwell hours for the age chart
}
```

### Section reference

#### `meta`
```json
"meta": {
  "version": 15,
  "report_date": "May 26, 2026",
  "report_as_of": "As of May 26, 2026",
  "topbar_label": "MVP Launch · As of May 26, 2026",
  "q1_weeks_remaining": 2,
  "revenue_assumption_per_vm": 2500,
  "revenue_assumption_label": "$2,500 = 1 VM migrated (program-standard assumption)"
}
```
`report_as_of` drives the data footer. `topbar_label` drives the topbar right side. `revenue_assumption_per_vm` feeds the revenue model calculation.

---

#### `funnel`
Source: Eloqua pipeline pull.
```json
"funnel": {
  "total_in_funnel": 264,
  "eloqua_pipeline": 169,
  "cs_intake_recent": 95,
  "cs_intake_label": "May 7–12",

  "stage_known_wip": 16,
  "stage_known_alltime": 71,
  "stage_known_avg_age_h": 19.2,

  "stage_validation_wip": 28,
  "stage_validation_alltime": 70,
  "stage_validation_avg_age_h": 28.8,

  "stage_mel_wip": 43,
  "stage_mel_alltime": 41,
  "stage_mel_avg_age_h": 9.6,

  "stage_po_wip": 0,
  "stage_po_alltime": 0,

  "recycled": 1,
  "disqualified": 0,

  "attrition_unknown_to_known": [153, 71],
  "attrition_known_to_validation": [71, 70],
  "attrition_validation_to_mel": [70, 41],
  "attrition_mel_to_po": [41, 0]
}
```
Attrition arrays are `[entries, exits_to_next_stage]`. The dashboard computes the loss percentage from these.

---

#### `targets`
Source: Program agreement. Static except for delivered counts.
```json
"targets": {
  "q1_target": 160,
  "q1_due": "Jun 12, 2026",
  "q1_delivered": 0,
  "q1_required_per_week": 40,

  "q2_target": 225,
  "q2_due": "Sep 11, 2026",
  "q2_delivered": 0,

  "q3_target": 255,
  "q3_due": "Dec 11, 2026",
  "q3_delivered": 0,

  "q4_target": 260,
  "q4_due": "Feb 20, 2027",
  "q4_delivered": 0,

  "total_program_target": 900,
  "total_delivered": 0
}
```
`q1_required_per_week` is manually calculated. Update it when weeks remaining changes.

---

#### `calls`
Source: NLPearl export.
```json
"calls": {
  "total_attempts": 239,
  "best_week_label": "Apr 27 week",
  "best_week_attempts": 183,
  "best_week_connected": 44,
  "pickup_rate_pct": 24,
  "sla_compliance_pct": 100,
  "avg_duration_min": null,

  "signal_strong": 17,
  "signal_weak": 20,
  "signal_negative": 0,
  "signal_no_connect": 6
}
```
`avg_duration_min` accepts a number or `null`. When `null`, the tile renders a placeholder.

---

#### `campaign`
Source: TechTarget/NetLine batch files.
```json
"campaign": {
  "total_leads": 940,
  "report_period": "May 7–26, 2026",
  "spend_to_date": 79900,
  "budget_total": 99960,
  "budget_remaining": 20060,
  "cpl": 85,
  "email_opted_out_flag": true,
  "email_opted_out_note": "Pending confirmation with Allison",

  "offer_1_id": "w_awsi224",
  "offer_1_leads": 590,
  "offer_1_impressions": 173790,

  "offer_2_id": "w_awsi225",
  "offer_2_leads": 350,
  "offer_2_impressions": 46497,

  "consent_q5_pct": 100,
  "consent_q6_pct": 100,
  "c_level_pct": 66
}
```
Do not use commas inside numbers (`79900` not `79,900`). `email_opted_out_flag` is boolean - use `true` or `false`, not a string.

---

#### `gaps`
Free-text fields. Plain strings only - no HTML.
```json
"gaps": {
  "summary_hero_subtitle": "264 contacts in funnel · 0 POs delivered · ...",
  "open_gaps_text": "0 POs to ACE as of May 26. ...",
  "ai_section_hero_sub": "239 total attempts · 24% peak pickup rate · ...",
  "ace_upload_status": "In prep",
  "po_bottleneck_note": "17 Strong signal calls, 0 POs. ..."
}
```
`·` (middle dot) is used as a separator in hero subtitle strings. Use it consistently.

---

#### `chart_trend`
Weekly MEL, MQP, and PO volume. Append one value to each array each week.
```json
"chart_trend": {
  "weeks": ["Apr 13", "Apr 20", "Apr 27", "May 4", "May 12"],
  "mel":   [0, 0, 41, 41, 41],
  "mqp":   [0, 30, 70, 70, 43],
  "po":    [0, 0, 0, 0, 0]
}
```
All four arrays must be the same length. Week labels are the Monday of each week in `Mon DD` format.

---

#### `chart_pickup`
Weekly NLPearl pickup rate. Use `0` for weeks with no data.
```json
"chart_pickup": {
  "weeks": ["Apr 13", "Apr 20", "Apr 27", "May 4", "May 12"],
  "rates": [0, 0, 24, 0, 0]
}
```
`weeks` must match `chart_trend.weeks` in length and labels.

---

#### `chart_age`
Average stage dwell in hours. Updated from the Eloqua stage age report.
```json
"chart_age": {
  "known_h": 19.2,
  "validation_h": 28.8,
  "mel_h": 9.6,
  "po_h": 0
}
```
`po_h: 0` renders as no bar. Set to actual hours once POs are flowing.

---

### JSON rules

- Field names are case-sensitive. Do not rename them.
- Numbers: no quotes, no commas (`79900` not `"79,900"`).
- Strings: always wrapped in double quotes.
- Booleans: `true` or `false` - no quotes, no capitals.
- Null values: `null` - no quotes.
- Every line except the last in a section needs a trailing comma.
- Arrays must keep all elements the same length across related sections (`chart_trend` and `chart_pickup` weeks arrays must match).

### Validating JSON before committing

Paste the data block into [jsonlint.com](https://jsonlint.com) to validate before uploading. A syntax error will render the dashboard as a blank page.

### Version incrementing

`meta.version` is incremented automatically when using Save and Export in Edit Mode. If editing the file manually, increment it yourself so the data footer reflects the correct version number.

---



Single self-contained HTML file. No external JS libraries. No build tooling.

- **CSS custom properties** for theming (light/dark token swap on `[data-theme]`)
- **Vanilla JS** for navigation, chart rendering, search, edit mode, and tooltips
- **Canvas 2D** for all charts (trend, pickup, age, calls, burndown, velocity, cumulative, VM size, timeline, states)
- **Google Fonts CDN** for DM Sans, DM Mono, and Bebas Neue - the only external dependency

## File structure

```
index.html    # Dashboard (single file, everything included)
README.md     # This file
```

## Requirements

A modern browser. Chrome, Firefox, Safari, or Edge. No IE support.

GitHub Pages configured to deploy from `main` branch, root directory.
