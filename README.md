# SecureVault 🛡️

Share secrets that self‑destruct. SecureVault is a privacy‑first ephemeral storage app for temporary notes and files. Data is encrypted in transit and access is gated via Firebase Authentication. Secrets automatically expire (24–72 hours) and are removed.

Live demo: https://hawks-securevault-231125.netlify.app/


![Open SecureVault](https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=https://hawks-securevault-231125.netlify.app/)

Quick QR (opens the demo)

Table of contents
- Features
- Tech stack
- Quick start
- Firebase configuration
- Security rules (recommended)
- Deployment
- Usage
- Troubleshooting
- Contributing & License

Features
- Ephemeral storage: Secrets expire after a configurable duration (1–72 hours).
- Text & file secrets: Store secure notes or upload files (any type).
- Limits: 5 MB max per file, 50 MB total vault size per user (free tier).
- Auth: Email/password via Firebase Auth.
- Storage & metadata: Firebase Storage + Firestore with TTL (Time To Live).
- Modern UI: Tailwind CSS, lightweight, client-side-first.

Tech stack
- Frontend: HTML5 + Vanilla JavaScript (ES Modules)
- Styling: Tailwind CSS (via CDN)
- Icons: Lucide Icons
- Backend: Firebase (Authentication, Firestore, Storage)

Quick start
1. Clone the repo
   git clone https://github.com/your-username/secure-vault.git
   cd secure-vault
2. Add Firebase config (see below).
3. Serve a static server (required for ES modules):
   - VS Code: Live Server → Go Live
   - Python: python3 -m http.server 8000
   - Node: npx serve .

Firebase configuration
Create `firebaseConfig.js` in the project root with your Firebase web config:

```javascript
// firebaseConfig.js
export const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "SENDER_ID",
  appId: "APP_ID"
};
```

Security rules (recommended)
Firestore — restrict reads/writes to the authenticated owner:
'''
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /artifacts/{appId}/users/{userId}/vault_items/{document} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
'''

Storage — enforce ownership and 5MB per file:

'''
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /users/{userId}/{allPaths=**} {
      allow read: if request.auth != null && request.auth.uid == userId;
      allow write: if request.auth != null
        && request.auth.uid == userId
        && request.resource.size < 5 * 1024 * 1024;
    }
  }
}
'''

Enable Firestore TTL
- In Firestore → Data → TTL, create a policy:
  Collection group: `vault_items`
  Timestamp field: `ttlDate`

Storage limits enforced client-side
- Per-file: 5 MB
- Per-user vault: 50 MB (client uses metadata to calculate current usage)

Deployment
- Static-ready app (index.html + JS). Deploy to Netlify, Vercel, or GitHub Pages.
- For Netlify:
  1. Ensure `firebaseConfig.js` is included or provide runtime config.
  2. Drag & drop folder or connect the repo → deploy.
- Live demo: https://hawks-securevault-231125.netlify.app/

QR code option
- The README embeds a simple QR via https://api.qrserver.com. Replace that image URL with your own hosted QR image if preferred.
- To generate a custom QR: https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=YOUR_URL

Usage notes
- Create an account (email/password) or sign in.
- Upload files (max 5MB) or paste a secure note.
- Choose a self‑destruct duration (1–72 hours). The app sets `ttlDate` on Firestore for auto-deletion.
- Manual deletion removes both Firestore metadata and the Storage object (if present).

Troubleshooting
- "Config missing": create `firebaseConfig.js` with correct values and reload.
- Upload fails: check file size and storage rules; ensure vault quota not exceeded.
- Auth errors: enable Email/Password provider in Firebase Console.

Contributing
- PRs and issues welcome. Keep changes small and document behavior.
- Respect privacy-first design choices.

License
- MIT License. See LICENSE file.

Contact
- Feedback / bug reports: feedback@securevault.app
