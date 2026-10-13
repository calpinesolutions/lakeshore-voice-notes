# ER WOYZ Developer Handover

Generated: 2026-10-08 IST

Repository: https://github.com/drgigy/woyz-notes-Aster

This handover package is a static web application for ER WOYZ. There is no build step and no server-side application code in this repository. The app uses Firebase Authentication, Cloud Firestore, static HTML pages, and Gemini API calls from the browser.

## Main Files

- `user.html` - doctor/user page for templates, voice dictation, Gemini note generation, OCR patient prefill, doctor credential QR scanning, drafts, save/delete, add-ons, discharge summaries, reply letters, and medical certificates.
- `index.html` - admin review page for assigned notes. Admins can review, copy, print, and manage IP discharge state.
- `log.html` - activity log page with streams for users, notes, admin, master, and Gemini API use.
- `master-admin.html` - group and admin assignment page.
- `qr-generator.html` - standalone doctor credential QR generator. Generated QR text format: `Aster@doc1,Doctor Name,Employee ID`.
- `firebase-config.js` - Firebase web app configuration.
- `firestore.rules` - Firestore security rules.
- `firebase.json`, `.firebaserc`, `.firebaserc.example` - Firebase deployment configuration.
- `manifest.webmanifest`, `sw.js`, `icons/` - PWA support files.
- `output/pdf/ER_WOYZ_Workflow_User_Guide_FAQ.pdf` - A4 workflow/user/FAQ guide.

## Data Model

Primary notes are stored under:

```text
users/{firebaseAuthUid}/notes/{noteId}
```

Other important collections:

- `users/{uid}` - profile, user settings, device name, doctor credential data.
- `groups/{groupId}` - master-admin group mappings.
- `activityLogs/{logId}` - append-only logs used by `log.html`.

## Important Workflow Notes

- User page opens on today's date.
- Selecting a template creates a new draft.
- Voice recording finishes by sending audio to Gemini and saving generated text back to the draft.
- Save stores manual edits and returns to Home.
- OCR patient label capture is optional. OCR capture alone should not create a draft; the draft is created when a template is selected.
- Pressing Home after OCR capture discards the captured OCR credential.
- Doctor credential is optional and per signed-in user.
- Admin `Copy note` copies content only.
- Admin `Review` saves edits, marks the note reviewed, clears draft wording, and removes the unreviewed red state.
- `qr-generator.html` is only for creating doctor credential QR codes and is not connected to patient records.

## Firebase Deployment

Install and sign in to Firebase CLI, then run from this folder:

```bash
firebase login
firebase deploy --only firestore:rules
firebase deploy --only hosting
```

If setting up a new Firebase project, copy `.firebaserc.example` to `.firebaserc`, set the project ID, enable Email/Password authentication, create users, and publish `firestore.rules`.

## Recommended Checks Before Deploy

```bash
git status --short
git diff --check
```

Parse inline scripts:

```bash
node --experimental-vm-modules --input-type=module -e "import fs from 'fs'; import vm from 'vm'; for (const file of ['user.html','index.html','log.html','master-admin.html','qr-generator.html']) { const html=fs.readFileSync(file,'utf8'); const scripts=[...html.matchAll(/<script(?![^>]*\\bsrc=)[^>]*>([\\s\\S]*?)<\\/script>/gi)]; for (let i=0;i<scripts.length;i++){ const code=scripts[i][1]; if (/^\\s*import\\s/m.test(code)) new vm.SourceTextModule(code, {identifier:file+':script'+i}); else new vm.Script(code, {filename:file+':script'+i}); } console.log(file, scripts.length, 'inline scripts parsed'); }"
```

## Live Workflow Test Checklist

- Doctor/user login.
- Mobile Home opens today by default.
- Visit Note template creates a fresh draft.
- Microphone recording, Finish, Gemini transcription, and auto-save.
- OCR Scan QR prefill then template selection.
- Home after OCR discards captured credentials.
- Discharge Summary generation.
- Reply Letter and Medical Certificate generation.
- Add-on/review dictation.
- Admin note visibility by group.
- Admin Copy note does not change note review color/status.
- Admin Review changes the note to reviewed.
- Master admin group assignment.
- Activity log access and Gemini API use table.
- QR generator login, generate, download, and print.

## Package Notes

This zip intentionally excludes `.git`, OS metadata, previous zip files, and local temporary files. To develop from this package, unzip it into a new folder and initialize or connect Git as needed.
