<div align="center">
  <img src="HHMS_Icon.svg" alt="HHMS Icon" width="96" />

  <h1>HHMS — Hose & Nozzle Replacement Tracker</h1>
  <p><strong>Hanging Hardware Management System</strong></p>
  <p>
    <img alt="Version" src="https://img.shields.io/badge/version-1.5.2-blue?style=flat-square&color=00205B" />
    <img alt="PWA" src="https://img.shields.io/badge/PWA-ready-green?style=flat-square" />
    <img alt="Android" src="https://img.shields.io/badge/Android-APK-brightgreen?style=flat-square&logo=android" />
    <img alt="Backend" src="https://img.shields.io/badge/backend-Google%20Apps%20Script-orange?style=flat-square&logo=google" />
    <img alt="License" src="https://img.shields.io/badge/internal-BPCL%20only-red?style=flat-square" />
  </p>
  <p>
    A zero-infrastructure field-operations app for <strong>Bharat Petroleum Corporation Limited</strong>.<br/>
    Digitises the full hose & nozzle replacement lifecycle across retail outlets — from field request to installation confirmation — with Google Sheets as the backend.
  </p>

  <p>
    <a href="https://pro-bhupesh.github.io/BPCL_HHMS/">🌐 Live App</a> &nbsp;·&nbsp;
    <a href="#-backend-setup-10-minutes">⚙ Backend Setup</a> &nbsp;·&nbsp;
    <a href="#-build-android-apk">📱 Build APK</a> &nbsp;·&nbsp;
    <a href="#-changelog">📋 Changelog</a>
  </p>
</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [User Roles](#-user-roles)
- [Features](#-features)
- [File Structure](#-file-structure)
- [Backend Setup (10 minutes)](#-backend-setup-10-minutes)
- [Upgrading the Backend](#-upgrading-the-backend)
- [Build Android APK](#-build-android-apk)
- [Demo Credentials](#-demo-credentials)
- [Changelog](#-changelog)
- [Author](#-author)

---

## 🔍 Overview

HHMS replaces a paper-based process where MSTs (Multi Skill Technicians) would manually log hose and nozzle replacement requests at BPCL petrol outlets. The app creates a **digital ticket** for every replacement, routes it through an approval and dispatch workflow, and closes it when the MST confirms the physical installation — optionally with photo evidence.

Everything runs on infrastructure your team already has: **GitHub Pages** hosts the single-file frontend, and **Google Sheets** is the database, accessed through a deployed **Google Apps Script Web App**.

No servers. No monthly fees. No app store approval required.

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      FRONTEND                           │
│   index.html  (single-file PWA — GitHub Pages)         │
│   • Pure HTML / CSS / JS — zero dependencies           │
│   • Flaticon uicons for iconography                     │
│   • Works as browser app, PWA, or Android APK (TWA)    │
└────────────────────┬────────────────────────────────────┘
                     │  HTTPS POST (JSON)
                     ▼
┌─────────────────────────────────────────────────────────┐
│                     BACKEND                             │
│   BPCL_AppsScript_Code.gs  (Google Apps Script)        │
│   • Deployed as a Web App (Execute as: Me)             │
│   • Token-based auth — 12-hour sliding sessions        │
│   • Reads / writes Google Sheets as the database       │
└────────────────────┬────────────────────────────────────┘
                     │  Sheets API
                     ▼
┌─────────────────────────────────────────────────────────┐
│                    DATABASE                             │
│   Google Sheets  ("BPCL HHMS Tracker")                 │
│   Tabs: Tickets · Outlets · Users · Stock              │
│         Config  · AuditLog                             │
└─────────────────────────────────────────────────────────┘
```

---

## 👥 User Roles

| Role | Who | Responsibilities |
|------|-----|-----------------|
| **Engineering Officer** | Territory officer | Reviews & approves ticket requests, manages outlets/users/stock, views all reports |
| **MST** (Multi Skill Technician) | Field technician | Raises replacement tickets, uploads installation photos, confirms completed work |
| **Warehouse** | Stores / dispatch team | Receives approved tickets, marks items as dispatched, tracks stock inventory |

---

## ✨ Features

**Ticket Lifecycle**
- Raise → Approve / Reject → Dispatch → Install (with photo) → Close
- 90-day outlet history flag during approval — warns if a replacement was done recently
- Every state transition is time-stamped and logged

**Dashboards**
- Role-specific dashboards with live stat cards (tap to navigate on mobile)
- Replacement trend chart — last 6 months, grouped by Approved / Rejected / Pending
- Recent tickets list on home screen

**Reports**
- Outlet Replacement History — full per-outlet timeline
- Frequency Analysis — which outlets and items replace most
- Monthly Consumption — hoses and nozzles issued per month
- CSV export on every report and list view

**Stock Management**
- PO-level stock receiving
- Live balance per item (Hose / Nozzle)
- Low-stock warning on the warehouse dashboard

**Mobile-First UI**
- Bottom navigation bar for thumb-friendly access on phones
- Ticket card view on mobile — replaces cramped horizontal tables
- Full-screen modal on mobile, hamburger sidebar on desktop
- Photo capture and compression (<1 MB) for installation evidence
- PWA installable from browser on both Android and iOS

**Security**
- Role-based access — each user sees only their relevant screens
- Sheet protection via `lockSheets` — direct edits blocked; only the script owner can modify the Sheet
- 12-hour session tokens with sliding expiry
- Password change enforced after first login (recommended)

---

## 📁 File Structure

```
HHMS/
├── index.html                  # Single-file PWA frontend (v1.5.2)
├── BPCL_AppsScript_Code.gs     # Google Apps Script backend (v1.5.0)
├── manifest.json               # PWA web manifest
├── twa-manifest.json           # Bubblewrap Android TWA config
├── BUILD_APK.md                # Step-by-step Android APK build guide
├── HHMS_Icon.png               # App icon (192×192 & 512×512)
├── HHMS_Icon.svg               # App icon (vector)
├── HHMS_Logo.png               # Full logo (PNG)
├── HHMS_Logo.svg               # Full logo (vector)
├── bg_PC.png                   # Login page background — desktop
├── bg_mobile.png               # Login page background — mobile
└── BPCL_Cloud_Setup_Guide.docx # End-user setup guide (for officers)
```

---

## ⚙ Backend Setup (10 minutes)

> Do this once. The same deployed URL is then used by all users.

**1. Create the Google Sheet**

Go to [Google Sheets](https://sheets.google.com), create a new spreadsheet, and name it **`BPCL HHMS Tracker`**.

**2. Open Apps Script**

Inside the sheet: **Extensions → Apps Script**.

Delete the default code and paste the entire contents of `BPCL_AppsScript_Code.gs`. Save the project (`Ctrl+S`) and name it **`BPCL HHMS Backend`**.

**3. Run the setup function**

From the function dropdown (top toolbar), select `setupSpreadsheet` and click **Run**.

When Google asks for permissions, click **Advanced → "Go to BPCL HHMS Backend (unsafe)" → Allow**. This is normal for personal scripts — it simply means the script hasn't gone through Google's formal review.

Six sheet tabs will be created and seeded with sample data.

**4. Lock the sheets**

Select `lockSheets` from the dropdown and click **Run**. This blocks direct edits by anyone except the script owner. The app still works fine because it runs as you.

**5. Deploy as a Web App**

**Deploy → New deployment → ⚙ (gear) → Web app**

| Setting | Value |
|---------|-------|
| Description | `HHMS Backend v1.5.0` |
| Execute as | **Me** |
| Who has access | **Anyone** |

Click **Deploy** and copy the Web App URL.

**6. Connect the frontend**

Open the HHMS app. On first launch you'll see a setup screen — paste the URL, click **Test & Connect**, then log in with the demo credentials below.

> The URL is already baked into `index.html` as the default for the BPCL Belgaum deployment. Skip this step if the URL hasn't changed.

---

## 🔄 Upgrading the Backend

When a new `BPCL_AppsScript_Code.gs` version is released:

1. Open your existing Apps Script project and replace all code with the new file contents. Save.
2. Run `migrateAddStockSheet` once — it adds any missing columns or sheets without touching existing data.
3. Run `lockSheets` once.
4. **Deploy → Manage deployments → ✏ Edit → Version: New version → Deploy.**

The Web App URL stays the same — no change is needed in the frontend.

---

## 📱 Build Android APK

The app can be packaged as a native Android APK using [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap) (Trusted Web Activity). A pre-filled `twa-manifest.json` is included so you can skip the interactive setup.

See **[BUILD_APK.md](BUILD_APK.md)** for the complete step-by-step guide including prerequisites, keystore management, full-screen asset links setup, and troubleshooting.

**Quick summary:**

```bash
# Prerequisites: Node 18+, JDK 17, Bubblewrap CLI
npm install -g @bubblewrap/cli

mkdir hhms-apk && cd hhms-apk
# Copy twa-manifest.json here, then:
bubblewrap update   # First run downloads Android SDK (~10 min)
bubblewrap build
# → app-release-signed.apk  (install on phone)
# → app-release-bundle.aab  (Play Store upload)
```

> **Future updates to `index.html` don't require a new APK.** The APK loads the live GitHub Pages URL on every open.

---

## 🔑 Demo Credentials

Seeded by `setupSpreadsheet`. **Change passwords after first login.**

| User ID | Password | Role |
|---------|----------|------|
| `bhupesh.bajiya` | `bpcl@123` | Engineering Officer — Belgaum Territory |
| `amit.sharma` | `bpcl@123` | MST (Multi Skill Technician) |
| `pooja.nair` | `bpcl@123` | MST (Multi Skill Technician) |

---

## 📋 Changelog

### v1.5.2 — May 2026 (Mobile UI)
- Bottom navigation bar — thumb-friendly nav on mobile
- Ticket card view on mobile — replaces cramped horizontal scroll table
- Stat cards tappable with icons — tap navigates to the relevant list
- Flaticon uicons throughout — nav, buttons, topbar, stat cards
- Real photo backgrounds on login page (`bg_PC.png` / `bg_mobile.png`)
- HHMS icon replaces BPCL text in topbar and login
- Auth token persists across page reloads (`localStorage`)
- Auto-clears stale API URL from `localStorage` on load

### v1.5.1 — May 2026 (Patch)
- Cloud Sync URL baked in — MSTs skip setup screen and go straight to login

### v1.5.0 — May 2026
- Install workflow for MST with photo evidence (camera capture + compression)
- Stock inventory with PO-level tracking (`Stock` sheet)
- Outlet history shown during approval with 90-day recurrence flag
- Sheet protection via `lockSheets` — direct edits blocked
- Hamburger sidebar nav on mobile, full-screen modals
- CSV export on every report and list view
- External `manifest.json` for PWA and Bubblewrap APK build

---

## 👤 Author

**Bhupesh Bajiya**  
Engineering Officer — Belgaum Territory  
Bharat Petroleum Corporation Limited  
📧 bpbajiya2016@gmail.com

---

<div align="center">
  <sub>© 2026 Bharat Petroleum Corporation Limited. For authorised BPCL personnel only.</sub>
</div>
