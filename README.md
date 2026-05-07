# BPCL_HHMS
BPCL
Energising Lives
Hose & Nozzle Replacement Tracker
Cloud Setup Guide
Google Sheets + Apps Script + Drive
Prepared for: Engineering Officer, BPCL Retail
Document version 1.0  |  May 2026
 
1. Overview
This guide walks you step-by-step through connecting the BPCL Hose & Nozzle Replacement Tracker app to a Google Sheet. Once set up, every ticket your field engineers raise — and every approval, dispatch, and photo — will be stored in the cloud automatically. No data lives only on a single laptop, and your team can work from anywhere.
You do not need to write any code. You only need to copy and paste two pieces of code, click a few buttons in Google, and paste one URL into the app.
What you will end up with
•	A single Google Sheet that holds all outlets, tickets, users, and history.
•	A Google Drive folder where every photo of a damaged hose or nozzle is saved automatically.
•	A web link (Apps Script URL) that the app talks to.
•	Three roles working together: Engineering Officer, Field Engineer, Warehouse — each with their own login.
How the pieces fit together
Component	Job	Lives in
Tracker App (HTML)	The interface your team uses on phone or laptop. Login, raise tickets, approve, dispatch, view reports.	On each user's device
Google Apps Script	The bridge. Receives requests from the app, reads/writes the Sheet, saves photos to Drive, checks who is allowed to do what.	Google Cloud (free)
Google Sheet	The database. Five tabs: Outlets, Tickets, Users, Timeline, Config.	Your Google Drive
Drive Folder	Stores every uploaded photo. The photo link is saved into the ticket row.	Your Google Drive
 
Time required: About 20–25 minutes the first time. After that, day-to-day use needs zero technical work.
Cost: Free. Google Apps Script and Drive are part of any standard Google account.
 
2. Before you start
Make sure you have these ready:
•	A Google account (a personal Gmail works; a BPCL Workspace account is better if you have one).
•	The two files delivered with this guide:
◦	BPCL_Hose_Nozzle_Tracker.html — the app itself.
◦	BPCL_AppsScript_Code.gs — the backend code you will paste into Google.
•	A modern browser (Chrome, Edge, or Firefox).
•	About 25 minutes of uninterrupted time.
 
3. Step-by-step setup
Follow these eight steps in order. Do not skip any.
Step 1 — Create the Google Sheet
1.	Open your browser and go to https://sheets.google.com
2.	Click the big + (Blank spreadsheet) to create a new sheet.
3.	Click the title at the top-left (it says "Untitled spreadsheet") and rename it to:
BPCL Hose & Nozzle Tracker
4.	Leave the sheet open. You will come back to it.
Step 2 — Open the Apps Script editor
5.	Inside that same Sheet, click the menu Extensions → Apps Script.
6.	A new tab opens with a code editor. The file shown is named Code.gs and contains a sample function function myFunction(). Select all of that sample code and delete it.
7.	Rename the project (top-left, where it says "Untitled project") to:
BPCL Tracker Backend
Step 3 — Paste the backend code
8.	Open the file BPCL_AppsScript_Code.gs (the one delivered with this guide) in any text editor — Notepad is fine.
9.	Select all of its contents (Ctrl+A) and copy (Ctrl+C).
10.	Switch back to the Apps Script tab and paste (Ctrl+V) into the empty editor.
11.	Click the floppy-disk Save icon (or press Ctrl+S).
Tip: If a popup asks you to grant permissions, accept. Apps Script needs access to your Sheet and Drive to do its job.
Step 4 — Run the one-time setup
This step creates the five tabs in your Sheet, fills in the column headers, adds a few demo users, and prepares everything.
12.	In the Apps Script editor, look at the dropdown at the top of the page (next to the Run / Debug buttons). It probably says doGet.
13.	Click that dropdown and choose:
setupSpreadsheet
14.	Click the Run button.
15.	A dialog appears asking for permission. Click Review permissions.
16.	Choose your Google account.
17.	Google may show "Google hasn't verified this app" — that is normal because it is your own private script. Click Advanced, then Go to BPCL Tracker Backend (unsafe).
18.	Click Allow.
19.	The script runs. At the bottom of the editor you should see green "Execution log" lines saying things like "Created sheet: Outlets", "Created sheet: Tickets", and ending with "Setup complete."
20.	Switch back to the Sheet tab. You should now see five tabs at the bottom: Outlets, Tickets, Users, Timeline, Config — each with navy-blue headers.
If the run fails: Check that you pasted the entire code file. The very first line should start with /** and the last line should be a closing brace. Re-paste if anything looks cut off.
Step 5 — Deploy the script as a Web App
This is the step that turns the script into a URL the app can talk to.
21.	In the Apps Script editor, click Deploy (top-right) → New deployment.
22.	Click the gear icon next to "Select type" and choose Web app.
23.	Fill in the form exactly like this:
Field	Set to
Description	BPCL Tracker v1
Execute as	Me (your email address)
Who has access	Anyone
 
Why "Anyone": The app authenticates users with its own ID and password (which you control inside the Users tab). "Anyone" only means the URL is reachable — it does not mean your data is public. Without this setting, your field engineers' devices cannot reach the script.
24.	Click Deploy.
25.	Google shows a dialog with the Web app URL. It looks like this:
https://script.google.com/macros/s/AKfyc.....abc123/exec
26.	Click the copy icon next to the URL. Save it somewhere safe — Notepad, an email to yourself, anywhere. You will paste it in the next step.
27.	Click Done.
Step 6 — Connect the app to your script
28.	Open the file BPCL_Hose_Nozzle_Tracker.html in your browser (double-click the file).
29.	On the login screen, log in with the demo officer account:
◦	User ID:  bhavesh.bajiya
◦	Password: bpcl@123
30.	In the left sidebar, click Settings.
31.	Scroll down to the Cloud Sync section.
32.	Paste the Web app URL you copied in Step 5 into the Apps Script URL field.
33.	Click Test Connection. After a few seconds you should see a green ✓ Connected message.
34.	Click Save & Connect.
35.	The app will reload and ask you to log in again — this time against the cloud (your Google Sheet's Users tab).
If Test Connection fails: Double-check the URL ends with /exec (not /dev). Make sure you chose Anyone in Step 5. Re-deploy if needed.
Step 7 — Log in for real and import outlets
36.	Log in again with bhavesh.bajiya / bpcl@123. You are now connecting to the live Sheet.
37.	Open the Outlets section.
38.	Click Import RO List.
39.	Either upload a CSV file or paste your outlet list directly. Use the Download Template button if you need the column format.
40.	Click Preview, review the rows, then Confirm Import.
41.	Open your Google Sheet and check the Outlets tab — your retail outlets should now be there.
Step 8 — Add your real users
The script created four demo users. Replace them (or add to them) with your real team members.
42.	Open the Users tab in your Google Sheet.
43.	Edit existing rows or add new ones. Use this format:
User ID	Password	Name	Role	Role Label	Territory
bhavesh.bajiya	bpcl@123	Bhavesh Bajiya	officer	Engineering Officer	Vadodara
amit.sharma	bpcl@123	Amit Sharma	engineer	Field Engineer	Vadodara North
stores.vad	bpcl@123	Vadodara Stores	warehouse	Warehouse / Stores	Vadodara Depot
 
Important: The Role column must be exactly officer, engineer, or warehouse (lowercase). Anything else and that user cannot log in.
Security: Ask every user to change their password the moment they first log in. Inside the app: Settings → Change Password.
 
4. Roles and permissions
The script enforces who can do what. Even if someone shares the URL, they cannot do anything they aren't authorised for.
Role	Can do	Cannot do
Engineering Officer	Approve / reject tickets, import RO list, complete tickets, see all reports, manage users.	(Full access)
Field Engineer	Raise new tickets with photos and damage details, view their own tickets and status.	Approve, reject, dispatch, import.
Warehouse / Stores	See approved tickets, mark them as Dispatched once goods leave the depot.	Approve, reject, raise tickets, import.
 
5. The day-to-day workflow
Here is what a single damaged-hose replacement looks like end-to-end:
44.	Field Engineer visits the outlet, opens the app, taps Raise Ticket.
45.	Engineer enters Customer Code (the unique ID for that RO), selects Hose or Nozzle, picks make/model/part, writes the reason for damage, attaches one or more photos, and submits.
46.	The ticket appears in the Engineering Officer's queue with status Pending.
47.	Officer reviews details and photos. Clicks Approve (or Reject with a reason). Status becomes Approved.
48.	Warehouse sees the approved ticket. When the replacement leaves the depot, they click Mark Dispatched. Status becomes Dispatched.
49.	Once the engineer confirms installation, the Officer clicks Complete. Status becomes Completed.
50.	The full timeline (who did what, when) is stored in the Timeline tab of the Sheet for audit.
 
6. Demo accounts (for first-time testing)
These are created automatically by setupSpreadsheet. Use them to test the system before you hand it to your team.
User ID	Password	Name	Role
bhavesh.bajiya	bpcl@123	Bhavesh Bajiya	Officer
amit.sharma	bpcl@123	Amit Sharma	Engineer
pooja.nair	bpcl@123	Pooja Nair	Engineer
stores.vad	bpcl@123	Vadodara Stores	Warehouse
 
Critical: Change all four passwords before going live. These defaults are public knowledge — anyone with the app file could try them.
 
7. Troubleshooting
"Test Connection failed"
•	Make sure the URL ends with /exec — not /dev or anything else.
•	In Apps Script: Deploy → Manage deployments → confirm Who has access is Anyone.
•	Try opening the URL in a browser. You should see a small JSON response like {"ok":true,"pong":"..."}. If you get a Google sign-in page, the deployment access setting is wrong.
"Invalid credentials" when logging in
•	Check the Users tab in the Sheet. The User ID column is case-sensitive.
•	Make sure the Role column is exactly officer, engineer, or warehouse (lowercase).
•	If you forgot a password, just edit the Password column directly in the Sheet.
Photos not uploading
•	Check the Drive folder "BPCL Tracker - Damaged Item Photos". The script creates it automatically the first time a photo is uploaded.
•	If you see permission errors, re-run setupSpreadsheet from the Apps Script editor — it will reset the folder configuration.
•	Photos should be under 5 MB each. If your engineers are taking very large photos, ask them to use a normal phone camera setting (not pro/RAW).
"Setup script failed" (Step 4)
•	Re-paste the full code. Make sure no lines were cut off when copying.
•	Make sure you ran the function setupSpreadsheet — not doGet, doPost, or any other function.
•	If it still fails, click View → Logs in the editor. Copy any red error and search Google — most issues are permission-related and resolve after re-granting access.
Officer cannot see new tickets
•	Click the Sync button in the top-right of the app — it pulls fresh data from the Sheet.
•	Check the user is logged in as officer (top-right shows "Engineering Officer").
 
8. Daily use and maintenance
Backups
Your data is your Google Sheet. Google already keeps version history (File → Version history → See version history), but it's good practice to:
•	Once a month: File → Download → Microsoft Excel (.xlsx). Save it on your laptop or to BPCL email.
•	Once a quarter: download the entire Drive photo folder as a ZIP for offline archive.
Adding more outlets
Two ways:
•	Inside the app: Outlets → Import RO List (CSV upload or paste). Best for bulk additions.
•	Directly in the Sheet: open the Outlets tab and paste rows in. Re-Sync from inside the app to pick up the changes.
Removing or disabling a user
Open the Users tab and either delete the row, or change their Password to a random string. Their old session token remains valid for up to 12 hours, after which they are forcibly logged out.
Updating the script
If a new version of the backend is released:
51.	Open the Apps Script editor in your existing Sheet (Extensions → Apps Script).
52.	Replace all the code with the new version.
53.	Save (Ctrl+S).
54.	Click Deploy → Manage deployments → pencil icon on the existing deployment → Version: New version → Deploy.
55.	The URL stays the same — your app keeps working without any change.
 
9. Pre-rollout checklist
Before you hand the app to your field engineers, tick all of these:
•	Sheet created and renamed to "BPCL Hose & Nozzle Tracker".
•	Apps Script code pasted, saved, setupSpreadsheet run successfully.
•	Five tabs visible in the Sheet (Outlets, Tickets, Users, Timeline, Config).
•	Web app deployed with Anyone access.
•	URL pasted in the app's Settings → Cloud Sync, Test Connection succeeded.
•	Real RO list imported into Outlets tab.
•	Real users added to Users tab with correct roles.
•	All demo passwords changed.
•	Drive folder "BPCL Tracker - Damaged Item Photos" exists in your Drive.
•	Test ticket raised, approved, dispatched, completed end-to-end.
•	App file (HTML) shared with field engineers and warehouse staff (email or shared drive).
•	Setup URL configured on every team member's device (same Settings step).
 
Once all boxes are ticked, you are live. Welcome to a paperless hose & nozzle replacement system. 
 
10. Need help?
If something behaves unexpectedly, open the app's Settings page and click View Diagnostics — it shows the last successful sync, the URL it is talking to, and any recent errors. Send that information along with a description of what you were doing when the issue happened.
This document can be updated whenever the app or the Apps Script changes. Always check you are reading the version that matches your current deployment (look at the version note on the cover page).
 
— End of Setup Guide —
BPCL  •  Energising Lives
