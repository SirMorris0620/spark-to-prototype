# From Spark to Prototype — course server setup

The web app (`index.html`) works on its own in **preview mode**: every learner's progress is saved only in their browser. Connect this Google Apps Script server to get sign-ups, sign-in codes by email, synced progress, pre/post-tests scored on the server, certificates that can be verified, uploads to Google Drive, the Spark AI tutor, the teacher dashboard, classes, course managers, and unit locks.

Everything runs in **your own Google account** (Sheets for data, Drive for uploads). Time needed: about 20 minutes.

## 1. Create the Sheet and the script

1. Create a new Google Sheet, for example *From Spark to Prototype – course data*.
2. In the Sheet: **Extensions → Apps Script**. Delete the sample code and paste the whole of `Code.gs` from this folder. Save.
3. In the Apps Script editor, choose the function **`setup`** and press **Run**. Approve the permissions (Sheets, Drive, Gmail for sign-in codes, external requests for the AI). The tabs are created automatically.
4. Choose **`testEmail`** and press **Run** once. You should receive a test email. (This allows the server to send sign-in codes.)

## 2. Script properties (Project Settings → Script properties)

| Property | Required | What it does |
|---|---|---|
| `ADMIN_EMAILS` | yes | Comma-separated emails with full admin access, e.g. `you@gmail.com` |
| `ADMIN_PIN` | yes | The dashboard PIN for admins. Use 8+ characters and keep it private. |
| `GEMINI_API_KEY` | for Spark | A key from Google AI Studio. Without any AI key, Spark is switched off. |
| `GEMINI_MODELS` | no | Models tried in order. Default `gemini-2.5-flash,gemini-2.5-flash-lite` |
| `ANTHROPIC_API_KEY` | no | Backup AI provider, used only if Gemini fails |
| `CLAUDE_MODEL` | no | Backup model name, default `claude-haiku-4-5` |
| `AI_DAILY_LIMIT` | no | Questions per learner per day (default 30) |
| `AI_MIN_AGE` | no | Minimum age for Spark (default 0). Keep it equal to `CONFIG.AI_MIN_AGE` in the app. |
| `REQUIRE_EMAIL_CODE` | no | `true` (default): returning learners confirm a 6-digit code sent by email |
| `PASS_PCT` | no | Post-test pass mark for the certificate (default 70) |
| `MAX_POST_ATTEMPTS` | no | Post-test attempts (default 3) |
| `UPLOAD_FOLDER_ID` | no | Drive folder for uploads (created automatically if empty) |
| `ALLOWED_DOMAINS` | no | Limit sign-ups to email domains, e.g. `school.edu.ph` |

Never paste API keys or PINs into the web app, a chat, or a shared document.

## 3. Deploy

1. **Deploy → New deployment → Web app**.
2. *Execute as*: **Me**. *Who has access*: **Anyone**.
3. Copy the web app URL (it ends in `/exec`).
4. Open `index.html` in a text editor, find `API_URL: ""` near the top, and paste the URL between the quotes. Save.
5. After any later change to `Code.gs`: **Deploy → Manage deployments → Edit → Version: New version → Deploy** (the URL stays the same).

Optional: add a daily time-driven trigger for the function **`cleanup`** (removes expired sessions and codes).

## 4. Publish the web app

Upload `index.html` to any static host, for example **GitHub Pages** (a repository with `index.html` at the root; Settings → Pages → Deploy from branch). Share the link with learners.

## 5. Teach with it

- **Teacher dashboard**: open the app → side menu → *Teacher dashboard* (or `#/admin`). Sign in with an admin email and `ADMIN_PIN`.
- **Classes**: create a class in the dashboard; learners type its code at sign-up or later in *Settings → My class code*. You can also share `…/index.html#/new?class=CODE`.
- **Unit locks**: by default each unit opens after every module before it is complete (and after the pre-test). In the dashboard, set each unit to *Sequential*, *Open*, or *Locked* for **everyone**, a **class**, or **one learner** (learner beats class beats everyone). You can also turn the pre-test requirement on or off.
- **Course managers**: give a teacher access to only their classes. Add their email, class codes (or `*`), and a personal PIN; they sign in at the same dashboard. Managers see only their classes' learners and can change only their locks.
- **Present to class**: admins and managers see *▶ Present to class* on every module (arrow keys; press **P** for presenter view with notes and timers).

## What is stored

Tabs: Learners, Sessions (token hashes only), Codes (hashed), Progress, Events, Tests, Certificates, AI, AILog, Feedback, Uploads, Reports, Errors, Classes, Managers (salted PIN hashes only), Locks, AdminSessions. Learner uploads go to one Drive folder per learner. See the Data Privacy Statement in the app and adjust it (contact email, retention) to your setting before you launch. In the app, edit `COURSE_CONTACT` at the top of the script.

## Reusing the course for another group

Nothing is tied to one school: make a copy of the Sheet + script for each engagement (or use classes in one copy), set the properties, deploy, and paste the new URL into a copy of `index.html`. To rename the course, change `COURSE_TITLE` and `COURSE_SUBTITLE` near the top of `index.html`, and `COURSE_NAME` near the top of `Code.gs`.
