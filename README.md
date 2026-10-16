# China CSC Scholarship Outreach & Response Tracker

<div align="center">

[![Live Web App](https://img.shields.io/badge/Live_App-csctracker.netlify.app-00e676?style=for-the-badge&logo=netlify&logoColor=white)](https://csctracker.netlify.app/)
[![Google Apps Script](https://img.shields.io/badge/Backend-Google_Apps_Script-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://developers.google.com/apps-script)
[![Google Sheets](https://img.shields.io/badge/Database-Google_Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)](https://www.google.com/sheets/about/)
[![Gmail API](https://img.shields.io/badge/Sync-Gmail_API-EA4335?style=for-the-badge&logo=gmail&logoColor=white)](https://mail.google.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)

**An automated academic CRM and outreach dashboard for Chinese Government Scholarship (CSC / Silk Road / University Program) applicants.**

Connect your own Google Sheet and Gmail to use the hosted web app, track professor correspondence automatically, and export your data to Excel.

[Launch Web App](https://csctracker.netlify.app/) | [Report Issue](https://github.com/khizarlatif/CSC_Tracker/issues)

</div>

---

## What is CSC Tracker?

When applying for the Chinese Government Scholarship (CSC), reaching out to prospective supervisors across Chinese universities (Tsinghua, Peking, Fudan, USTC, Zhejiang University, Harbin Institute of Technology, SJTU, etc.) requires managing dozens of emails, follow-ups, and pre-acceptance letters.

**CSC Tracker gives you a hosted, modern dashboard that connects directly to your own Google Sheet and Gmail:**
- **Hosted App:** Ready to use immediately at [csctracker.netlify.app](https://csctracker.netlify.app/) (no installation or hosting required).
- **Your Data Stays Yours:** All professor records and email logs live inside your private Google Sheet and Gmail account.
- **Automated Tracking:** Background scripts scan for professor replies, clean email threads, and update interaction statuses automatically.
- **Excel Export:** Download your complete outreach pipeline as an Excel spreadsheet (`.xlsx`) with 1 click.

---

## Features

- **Live Outreach Analytics & KPIs:** Instant conversion metrics showing total professors contacted, response rate %, pre-acceptance count, and pending follow-ups.
- **Automated Gmail Synchronization:** Runs in the background every 8 hours to scan for professor responses and update interaction statuses.
- **Email Quote Cleaning:** Strips forwarded headers, quotation blocks, and mobile signatures so you only see the actual message content.
- **Status Pipeline:** Automatically classifies status into Not Contacted, Awaiting Reply, Action Required, Accepted, Ignored, Rejected, or Ghosted.
- **Offline Caching:** High-speed client cache using IndexedDB so the dashboard loads instantly.
- **1-Click Excel Backup:** Export your entire professor database directly into an Excel (`.xlsx`) workbook at any time.

---

## Quick Setup: How to Use with Your Own Google Sheet

You do **not** need to deploy or host any web application. The web app is already live at [csctracker.netlify.app](https://csctracker.netlify.app/). Follow these 3 steps to connect your own sheet and script:

### Step 1: Create Your Google Sheet
1. Create a new Google Sheet at [sheets.new](https://sheets.new).
2. Set the sheet tab name to `Sheet1`.
3. Add the following column headers in Row 1:
   ```text
   Professor | University | Research Interest | Email | Linked_Email | Email_Logs | Last_Contact | Thread_State | Notes | Reply_Status
   ```

### Step 2: Add the Background Script ("The Brain")
1. In your Google Sheet, click **Extensions** > **Apps Script**.
2. Replace any default code with the contents of [`backend_template.js`](backend_template.js).
3. At the top of the file, fill in your Google Sheet URL and your Gmail address:
   ```javascript
   var TRACKER_SHEET_URL         = "PASTE_YOUR_GOOGLE_SHEET_URL_HERE";
   var TRACKER_SHEET_NAME        = "Sheet1";
   var TRACKER_USER_EMAIL        = "your.email@gmail.com";
   var IGNORED_DAYS_THRESHOLD         = 14;
   var ACTION_REQUIRED_DAYS_THRESHOLD = 7;
   ```
4. Click **Deploy** > **New deployment**.
5. Select type **Web app**:
   - **Execute as:** `Me (your account)`
   - **Who has access:** `Anyone`
6. Click **Deploy**, authorize the permissions, and **copy your Web App URL** (looks like `https://script.google.com/macros/s/.../exec`).

#### Enable Automatic 24/7 Gmail Sync:
1. In the Apps Script left sidebar, click the **Triggers (clock icon)**.
2. Click **Add Trigger** (bottom right):
   - **Choose which function to run:** `syncRecent`
   - **Select event source:** `Time-driven`
   - **Type of time based trigger:** `Hour timer` -> `Every 8 hours`
3. Click **Save**.

### Step 3: Connect to the Live Web App
1. Open [csctracker.netlify.app](https://csctracker.netlify.app/).
2. Click the **API Settings** button in the navigation bar.
3. Paste your **Web App URL** from Step 2 and click **Save**.
4. That is it! Your personal dashboard is connected:
   - Add professors directly from the app or from your Google Sheet.
   - Click **Run Sync** to scan your Gmail on demand.
   - Click **Export to Excel** to download your spreadsheet.

---

## Status Classification Guide

| Status | Meaning |
| :--- | :--- |
| **Not Contacted** | Newly added professor profile; no email sent yet. |
| **Awaiting Reply** | Outreach email sent; waiting for professor reply (within 14 days). |
| **Action Required** | Professor has replied and awaits your answer, or follow-up is overdue (> 7 days). |
| **Accepted** | Pre-acceptance letter obtained or positive response received. |
| **Ignored** | No response received after 14 days. |
| **Rejected** | Professor unavailable or laboratory is full. |
| **Ghosted** | Communication stalled after initial discussion. |

---

## Privacy & Security

- **Your data stays in your control:** The web app runs client-side in your browser. All records are stored exclusively in your own Google Sheet and your own Gmail account.
- **No third-party database:** Nothing is stored on external servers or Netlify databases.

---

## License

Distributed under the MIT License. See `LICENSE` for more information.

<div align="center">

Built by [Khizar Latif](https://github.com/khizarlatif)

</div>
