# Benchline Progress Dashboard: Architecture Overview

---

## Data Flow

```
Google Docs                    Google Sheets                      Apps Script              Browser
─────────────                  ─────────────                      ───────────              ───────
Master Checklist  ──sync──►    SHEET_REGISTRY
                               Section sheets (S{N}_{TAG})
Daily Checklist   ──sync──►    Subtask sheets (D{A-F}_{N}_{TAG})  ◄──read──  code.gs  ──HtmlService──►  dashboard.html
                               Special sheets (MINDSET, TARGETS)
                               DAILY_PLAN (keyed by date)

Master Checklist  ◄──sync──    Section sheets                     ──write──  code.gs  ◄──doPost()──     dashboard.html
```

---

## Three Sync Operations

**Sync from Master Checklist**

Parses the Master Google Doc (Heading 1 sections with task tables) and writes each section as its own sheet in the spreadsheet. Updates SHEET_REGISTRY with each section's name, type (master), display name, and order. Running this sync adds new sections and removes deleted ones without any code changes.

**Sync from Daily Checklist**

Parses the Daily Google Doc (category headers A through F with subtask tables, plus special tables for mindset reminders, weekly targets, minimum viable day, and milestones) and writes each category as a subtask sheet using the naming pattern `D{A-F}_{N}_{TAG}`. Special sheets like MINDSET_REMINDER and DAILY_&_WEEKLY_TARGETS are created or overwritten from the doc.

**Sync to Google Doc**

Reads all section sheets and writes updated task statuses and notes back into the Master Google Doc tables with color coding by status (green for Done, orange for In Progress, red for Blocked). Optionally pushes the full checklist as a markdown file to GitHub via the GitHub API.

---

## SHEET_REGISTRY

Every active data sheet is registered in one central SHEET_REGISTRY sheet. The server reads this registry first, then loads all active sheets in a single batch. New sections added via sync are immediately available in the UI without code changes.

| Column | Purpose |
|---|---|
| SHEET_NAME | Exact name of the sheet in the spreadsheet |
| TYPE | master, daily_task, daily_subtask, or special |
| DISPLAY_NAME | Human-readable label shown in the UI |
| ACTIVE | TRUE/FALSE; inactive sheets are skipped on load |
| ORDER | Display order in the Sections tab |

---

## DAILY_PLAN Sheet

Stores each day's plan keyed by date. One row per selected task:

| Column | Purpose |
|---|---|
| DATE | Plan date (YYYY-MM-DD) |
| TASK_ID | Reference to the source task row |
| SHEET_NAME | Which sheet the task came from |
| TASK | Task text |
| SELECTED | Whether the task is included in today's plan |
| COMPLETED | Completion flag |
| MINUTES | Duration allocation in minutes |
| CATEGORY | Category A through F |
| START_TIME | HH:MM start time |
| NOTES | Per-task plan notes |

---

## Caching

The server wraps `getAllData()` in a 2-minute CacheService cache. This keeps the app responsive on repeated tab switches without hitting the Sheets API on every navigation. On any write operation (status update, note save, plan save), the cache is cleared so the next read fetches fresh data.

---

## Authentication

Password is stored as a value in the CONFIG sheet (KEY = PASSWORD). On login, the client sends the entered password to `doPost()`, which reads CONFIG and compares. On success, the password is stored in localStorage with a 1-hour expiry timestamp. On each page load, the client checks localStorage: a valid session skips the login screen; an expired or missing session shows it.
