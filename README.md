# ðŸ‡¨ðŸ‡³ China CSC Scholarship Outreach & Response Tracker

<div align="center">

[![Live Demo](https://img.shields.io/badge/Live_Demo-csctracker.netlify.app-00e676?style=for-the-badge&logo=netlify&logoColor=white)](https://csctracker.netlify.app/)
[![Google Apps Script](https://img.shields.io/badge/Backend-Google_Apps_Script-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://developers.google.com/apps-script)
[![Google Sheets](https://img.shields.io/badge/Database-Google_Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)](https://www.google.com/sheets/about/)
[![Gmail API](https://img.shields.io/badge/Sync-Gmail_API-EA4335?style=for-the-badge&logo=gmail&logoColor=white)](https://mail.google.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)

**An intelligent, full-stack CRM and automated outreach tracker built for students applying for the Chinese Government Scholarship (CSC / Silk Road / University Program).**

[Explore Live Dashboard â†—](https://csctracker.netlify.app/) â€¢ [Report Bug](https://github.com/khizarlatif/CSC_Tracker/issues) â€¢ [Request Feature](https://github.com/khizarlatif/CSC_Tracker/issues)

</div>

---

## ðŸ“Œ Overview

Reaching out to prospective PhD/Master's supervisors across top Chinese institutions (*Tsinghua, Peking, Fudan, USTC, Zhejiang University, Harbin Institute of Technology, SJTU, etc.*) is demanding. Keeping track of dozens or hundreds of email exchanges, follow-up deadlines, acceptance documents, and reply statuses in messy spreadsheets leads to missed opportunities.

**CSC Tracker** solves this with an automated, self-healing pipeline:
- **Frontend Dashboard:** A modern, ultra-responsive dark-mode web application hosted on **Netlify** with real-time KPI metrics, search, filtering, and thread inspection.
- **Backend Engine ("The Brain"):** A custom **Google Apps Script** running 24/7 in the cloud that scans Gmail, strips email quote clutter (bilingual English & Chinese), updates correspondence timelines, and manages candidate states.
- **Cloud Database:** A linked **Google Sheet** that serves as your single source of truth, accessible anywhere and always synced.

---

## âš¡ Key Features

- ðŸ“Š **Real-Time Outreach Analytics & KPIs**
  - Instant conversion metrics: Total Professors Contacted, Response Rate %, Acceptance Rate %, and Pending Follow-ups.
  - Interactive status badges with color-coded hierarchy (`Accepted`, `Action Required`, `Awaiting Reply`, `Ignored`, `Rejected`, `Ghosted`).

- ðŸ“¬ **Automated Gmail Bidirectional Synchronization**
  - Background cron runs every 8 hours scanning recent activity (`newer_than:2d`).
  - Intelligent auto-detection of professor responses vs. applicant follow-ups.
  - Filters out system mailer daemons, delivery failure notices, out-of-office notes, and automatic vacation replies (`è‡ªåŠ¨å›žå¤`, `ä¸åœ¨`, etc.).

- ðŸ‡¨ðŸ‡³ **Bilingual Email Body Cleaning & De-duplication**
  - Smart quote & signature stripper removes forwarded headers, Outlook signatures, and Chinese quotation blocks (`åŽŸå§‹é‚®ä»¶`, `äºŽ...å†™é“:`, `From: ...`).
  - Deduplicates identical message chunks for a clean, readable conversation timeline.

- â³ **Self-Healing Chunked Historical Sync**
  - Time-based recurring triggers safely bypass Google Apps Script's 6-minute execution quota when syncing hundreds of historical email threads.

- ðŸ” **Instant Search, Dynamic Filtering & Sorting**
  - Instant client-side search across Professor Name, University, Research Domain, and Email.
  - Multidimensional filtering by interaction state and date.

- ðŸ’¾ **Offline-First Caching & 1-Click Excel Export**
  - Uses `localforage` (IndexedDB) for zero-latency local caching.
  - Integrated `SheetJS (XLSX)` export for instant offline backups and reporting.

---

## ðŸ—ï¸ Architecture & Data Flow

```mermaid
flowchart TD
    subgraph UI ["ðŸŒ Client Layer (Netlify)"]
        A["Dashboard Web App<br/>(csctracker.netlify.app)"]
        B["Local Cache<br/>(IndexedDB / localforage)"]
        C["Excel Exporter<br/>(SheetJS)"]
        A <--> B
        A --> C
    end

    subgraph GAS ["ðŸ§  The Brain (Google Apps Script Engine)"]
        D["doGet / doPost REST API"]
        E["cleanBody() & dedupeLog()<br/>(Chinese + English Stripper)"]
        F["calculateStatus()<br/>(State Machine)"]
        G["Background Triggers<br/>(syncRecent & syncFullHistoryAll)"]
    end

    subgraph Google ["â˜ï¸ Google Workspace Cloud"]
        H[("Google Sheets<br/>(Primary Database)")]
        I["Gmail API<br/>(Sent / Received Threads)"]
    end

    A <== "REST / JSON (CRUD & Stats)" ==> D
    G --> I
    I --> E
    E --> F
    F --> H
    D <--> H
```

---

## ðŸ”„ State Machine & Status Lifecycle

The Apps Script engine automatically tracks and transitions the status of each professor interaction based on email timestamps and sender directions:

| Status | Trigger Condition | Visual Indicator |
| :--- | :--- | :---: |
| **Not Contacted** | Initial state; no emails sent or logged yet | `âšª Gray` |
| **Awaiting Reply** | Email sent to professor; within threshold (< 14 days) | `ðŸŸ¡ Amber` |
| **Action Required** | Professor has replied and requires applicant follow-up, or > 7 days since last exchange | `ðŸŸ  Orange` |
| **Accepted** | Positive reply or Pre-Acceptance Letter obtained (manually locked or detected) | `ðŸŸ¢ Bright Green` |
| **Ignored** | Sent email with no response exceeding threshold (> 14 days) | `ðŸŸ£ Purple` |
| **Rejected** | Professor unavailable or laboratory at full capacity | `ðŸ”´ Red` |
| **Ghosted** | Exchange abruptly stalled during mid-conversation | `ðŸ”µ Indigo` |

---

## ðŸš€ Setup & Deployment Guide

### 1. Set Up the Google Sheet
1. Create a new [Google Sheet](https://sheets.new).
2. Set the sheet name to `Sheet1`.
3. Add the following header columns in Row 1:
   ```text
   Professor | University | Research Interest | Email | Linked_Email | Email_Logs | Last_Contact | Thread_State | Notes | Reply_Status
   ```

### 2. Deploy Google Apps Script ("The Brain")
1. In your Google Sheet, navigate to **Extensions** > **Apps Script**.
2. Replace any default code with the contents of [`backend_template.js`](backend_template.js).
3. Update the top configuration parameters:
   ```javascript
   var TRACKER_SHEET_URL         = "YOUR_GOOGLE_SHEET_URL";
   var TRACKER_SHEET_NAME        = "Sheet1";
   var TRACKER_USER_EMAIL        = "your-email@gmail.com";
   var IGNORED_DAYS_THRESHOLD         = 14;
   var ACTION_REQUIRED_DAYS_THRESHOLD = 7;
   ```
4. Click **Deploy** > **New deployment**.
5. Select **Web app** as the deployment type:
   - **Execute as:** `Me (your account)`
   - **Who has access:** `Anyone` (or restricted with access token)
6. Copy the generated **Web App URL**.

### 3. Configure Background Triggers
In the Apps Script editor:
1. Open the **Triggers** tab (clock icon on the left panel).
2. Click **Add Trigger**:
   - **Function to run:** `syncRecent`
   - **Event source:** `Time-driven`
   - **Type of time based trigger:** `Hour timer` -> `Every 8 hours`
3. Click **Save** and grant the required Gmail and Google Sheets permissions.

### 4. Deploy the Frontend on Netlify
1. Fork or clone this repository.
2. In `index.html`, set the API endpoint URL to your deployed Google Apps Script Web App URL.
3. Deploy directly to [Netlify](https://www.netlify.com/):
   - Simply drag and drop the project folder, or connect your GitHub repository for continuous deployment.
4. Your tracker will be live and ready!

---

## ðŸ› ï¸ Tech Stack

- **Frontend:** Vanilla HTML5, CSS3 Modern Dark Duolingo/Linear aesthetic, ES6 JavaScript
- **Libraries:**
  - [SheetJS (xlsx.full.min.js)](https://cdn.sheetjs.com/) â€” Client-side spreadsheet export
  - [LocalForage](https://github.com/localForage/localForage) â€” Offline storage & high-speed IndexedDB cache
  - [Nunito Google Font](https://fonts.google.com/specimen/Nunito) â€” Clean typography
- **Backend Engine:** Google Apps Script (GAS) runtime with Google V8 engine
- **APIs & Services:** Gmail Service API, Google Sheets API, Netlify Hosting

---

## ðŸ¤ Contributing

Contributions, feedback, and feature suggestions are always welcome!
1. Fork the Project (`gh repo fork khizarlatif/CSC_Tracker`)
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## ðŸ“„ License

Distributed under the MIT License. See `LICENSE` for more information.

<div align="center">

Built with â¤ï¸ by [Khizar Latif](https://github.com/khizarlatif)

</div>
