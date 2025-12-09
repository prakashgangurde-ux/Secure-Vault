# SecureVault 🛡️

Share secrets that self-destruct. SecureVault is a privacy-first, ephemeral storage app for temporary notes and files. Data is encrypted in transit and access is gated via Firebase Authentication. Secrets automatically expire (24–72 hours) and are removed.

Live demo: https://hawks-securevault-231125.netlify.app/

Quick QR (opens the demo)
![Open SecureVault](https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=https://hawks-securevault-231125.netlify.app/)

Table of contents
- Features
- Tech stack
- Local development
- Firebase configuration
- Security rules (recommended)
- Deployment
- Usage
- Troubleshooting
- Contributing & License

Features
- Ephemeral storage: Secrets expire after a configurable duration (1 hour up to 72 hours).
- Text & file secrets: Store secure notes or upload files (any type).
- Free tier limits: 5 MB max per file, 50 MB total vault size per user.
- Auth: Email/password via Firebase Auth.
- Encryption & storage: Firebase Storage + Firestore for metadata and TTL (Time To Live).
- Modern UI: Tailwind + glassmorphism; lightweight, client-side-first.

Tech stack
- Frontend: HTML5 + Vanilla JavaScript (ES Modules)
- Styling: Tailwind CSS (CDN)
- Icons: Lucide
- Backend: Firebase (Authentication, Firestore, Storage)

Local development (quick)
1. Clone the repo:
   git clone https://github.com/your-username/secure-vault.git
   cd secure-vault
2. Provide a Firebase config file (see below).
3. Serve with a static server (required for ES modules):
   - VS Code: Live Server extension → Go Live
   - Python: python3 -m http.server 8000
   - Node: npx serve .

Firebase configuration
Create a file named `firebaseConfig.js` in the project root with your Firebase web config:

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

Important: Never commit production secrets to public repositories. Use environment-managed builds for deployments.

Firestore rules (recommended)
- Only allow authenticated users to read/write their own vault items.

Example:
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /artifacts/{appId}/users/{userId}/vault_items/{document} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}

Storage rules (recommended)
- Enforce authentication, ownership and file size limit (5MB).

Example:
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

Enable Firestore TTL
- In Firestore → Data → TTL, create a policy:
  Collection group: `vault_items`
  Timestamp field: `ttlDate`

Storage limits enforced client-side
- Per-file: 5 MB
- Per-user vault: 50 MB (client calculates existing usage from Firestore metadata before upload)

Deployment
- The project is static-ready (index.html + JS). Deploy to Netlify, Vercel or GitHub Pages.
- For Netlify:
  1. Build folder is the repo root (static).
  2. Ensure `firebaseConfig.js` is present on the deployed site (or supply runtime config).
  3. Drag & drop or connect repo → deploy.
- Demo hosted at: https://hawks-securevault-231125.netlify.app/

Usage notes
- Create account with email/password (sign-up flow available in UI).
- Upload files via the "File" tab (respect 5MB limit).
- Set a self-destruct timer (1–72 hours). The app sets a Firestore `ttlDate` for automatic deletion.
- You can manually destroy a secret via the UI — this deletes Firestore doc and storage object (if any).

Security & privacy considerations
- This UI assumes Firebase rules are correctly configured — enforce rules in the console before going public.
- The app does not implement end-to-end client-side encryption keys for zero-knowledge; it relies on Firebase security rules and HTTPS. Review threat model and add client-side encryption if required.
- Keep API keys and firebaseConfig out of public repos as appropriate.

Troubleshooting
- "Config missing" on startup: create `firebaseConfig.js` with correct values and reload the page.
- Upload fails: check file size, storage rules, and current vault usage (50MB limit).
- Authentication errors: ensure Email/Password provider is enabled in Firebase Console.

Contributing
- PRs and issues welcome. Keep changes small and document behavior.
- Respect privacy-first design choices when adding features.

License
- MIT License. See LICENSE file.

Contact
- Feedback / bug reports: feedback@securevault.app