# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file employee performance dashboard for **Houston Centerless Grinding Service LLC**. Everything (HTML, CSS, JS) lives in `index.html` — there is no build system, bundler, or framework. Open the file directly in a browser to run it.

## Architecture

### Data Layer (JS globals at top of `<script>`)
- `employees[]` — master list with `code`, `name`, `hourlyRate`
- `records[]` — monthly performance entries with hours and payroll fields
- `shopRate` — dollars-per-hour used to calculate value produced (default $126)
- All data persists to **localStorage** under key `hcg_dashboard_v2`
- On load, `loadData()` merges stored data with hardcoded defaults (new defaults not in storage are appended)

### Computed Metrics (`computeRow()`)
- **Job Efficiency %** = Hrs Completed / Hrs on Jobs × 100
- **Employee Efficiency %** = Hrs Completed / Hrs Worked × 100 (this is the bonus basis)
- **Pay** = (Hrs Worked − OT) × hourlyRate + OT × hourlyRate × 1.5
- **Value Produced** = Hrs Completed × shopRate
- **Net Value** = Value Produced − Pay
- **Bonus tiers** based on Emp Eff %: <70%=$0, 70-79%=$200, 80-89%=$300, 90-99%=$400, 100%+=$500

### UI Tabs
1. **Dashboard** — metric cards, Chart.js bar chart, gauge detail panel, sortable performance table. Filtered by period (month/quarter/YTD/all).
2. **Employees** — CRUD for employee master list (code, name, hourly rate).
3. **Performance Data** — CRUD for monthly records. Supports single-record and batch entry (all employees for a month at once).

### Key Patterns
- No modules or imports — all functions are global. DOM manipulation is vanilla JS.
- Chart.js 4.4.1 loaded via CDN for the bar chart; gauge is a hand-drawn SVG.
- `refreshDashboard()` is the main render entry point — calls `updateMetrics()`, `updateBarChart()`, `updateTable()`.
- `aggregateByEmployee()` groups filtered records by employee code, then pipes through `computeRow()`.
- Period filtering: `getFilteredRecords()` returns records matching the current `periodType`/`periodValue` globals.
- Table sorting controlled by `sortCol`/`sortDir` globals; click column headers to toggle.

### CSS
- Dark theme using CSS custom properties (`:root` vars). Print stylesheet overrides to light theme.
- Two font families: Roboto Condensed (UI) and JetBrains Mono (data/numbers), both from Google Fonts CDN.
