# Benchline Progress Dashboard

**Google Apps Script | Productivity Dashboard | Task Management**

[![🌐 Live App](https://img.shields.io/badge/🌐_Live_App-03a6a5)](https://benchlineanalytics.com/progress-dash)

A password-protected daily productivity tracker and task management system built with Google Apps Script and served via a custom domain. It combines a master task backlog synced from Google Docs with daily time-blocked planning, real-time status tracking, and a vertical calendar view. Designed for solo entrepreneurs managing multiple parallel work streams.

---

# Project Overview

No single off-the-shelf tool connected a full task backlog, daily time allocation, and real-time completion tracking in one mobile-friendly view. This app imports tasks from Google Docs, lets you plan each day by assigning category time blocks with start times, then tracks completion live with status cycling and progress rings.

[![🏗️ Architecture Overview](https://img.shields.io/badge/🏗️_Architecture_Overview-05467c)](Architecture_Overview.md)

---

# Dashboard Tabs

1️⃣ **Overview:** Daily completion ring (%), category breakdown bars showing task status distribution, vertical time-block calendar rendering the day as absolute-positioned blocks with start times and durations, Time Remaining countdown, current phase focus, weekly targets, and Upwork milestone tracker.

2️⃣ **Sections:** Full master task list organized by section, imported from a Google Doc. Expandable cards per section with completion percentage, progress bar, and task count. Inline note editing per task (pencil icon). 5-status cycling: To Do, In Progress, Blocked, Done, TBD.

3️⃣ **Active:** Filtered view showing only tasks with In Progress or Blocked status. Compact card layout per task with section name, task text, status badge, and notes. Quick access to active work without scrolling the full section list.

4️⃣ **Weekly:** Weekly review question navigator: one question at a time with Prev/Next navigation and a progress counter. Per-question notes (textarea). Mark Done checkbox per question. Daily and weekly targets display.

5️⃣ **Daily:** Category time allocation (A through F, each color-coded with a custom name). Per-category inputs: start time (HH:MM picker) and duration in minutes with auto-computed end time. Subtask selection checkboxes to build today's plan. Save, Edit Mode (inline task edits and ad-hoc task creation), Change Plan, and Cancel Day controls.

---

# Key Features

| Feature | Description |
|---|---|
| Vertical Time-Block Calendar | Absolute-positioned blocks inside a 24-hour container; tap to expand category details; falls back to horizontal stacked bar if no start times are set |
| Google Docs Sync | Bidirectional: import task structure from Master Doc and Daily Doc; sync updated statuses and notes back to Master Doc |
| SHEET_REGISTRY Architecture | A single registry sheet drives all data loading; no hardcoded sheet names, column indices, or row numbers anywhere in the codebase |
| Optimistic UI | Status and note changes apply instantly client-side; revert automatically on server failure with a toast notification |
| Server-Side Caching | 2-minute CacheService cache on all data reads; automatically cleared on any write operation |
| 5-Status Task System | To Do, In Progress, Blocked, Done, TBD; click any status badge or checkbox to cycle forward |
| Session Auth | Password stored in CONFIG sheet; 1-hour localStorage TTL; auto-login on page reload while session is valid |
| Daily Auto-Reset | On first load each day, all completed daily subtasks reset from Done to To Do |

---

# How It Was Built

Single two-file architecture: `code.gs` (Google Apps Script server) handles all sheet reads, writes, caching, and Google Docs parsing via a `doPost()` action dispatch pattern. `dashboard.html` (vanilla HTML/CSS/JavaScript) renders the full single-page app via Google HtmlService. Google Sheets acts as the database using a registry-driven loading pattern; Google Docs are the canonical task source of truth. Deployed as a Google Apps Script web app served at a custom domain.

---

# Tools Used

- **Backend:** Google Apps Script
- **Frontend:** Vanilla HTML5, CSS3, JavaScript (no frameworks)
- **Database:** Google Sheets (SHEET_REGISTRY + section sheets + DAILY_PLAN)
- **Task Source:** Google Docs (Master Checklist, Daily Checklist)
- **Caching:** Google CacheService (server-side, 2-minute TTL)
- **Optional:** GitHub API (checklist push to version control)

---

# Author

**Sawandi Kirby**

Data Analytics & Business Intelligence
Benchline Analytics - Data intelligence for organizations that mean business.

- GitHub: https://github.com/visualkirby
- LinkedIn: https://linkedin.com/in/sawandi-kirby
- Kaggle: https://kaggle.com/sawandikirby
