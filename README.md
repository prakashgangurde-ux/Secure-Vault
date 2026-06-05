# SecureVault 🛡️

A privacy-first secure file and note sharing platform that automatically destroys stored content after a specified expiration period.

SecureVault enables users to securely store sensitive notes and files with Firebase Authentication, Firestore TTL policies, and Firebase Storage integration.

## Live Demo

https://hawks-securevault-231125.netlify.app/

---

## Overview

SecureVault is designed for users who need temporary and secure storage for confidential information.

Unlike traditional cloud storage platforms, SecureVault automatically removes stored content after a user-defined expiration period, reducing long-term exposure risks.

### Use Cases

* Temporary password sharing
* One-time document exchange
* Secure personal notes
* Temporary project collaboration
* Short-lived file storage

---

## Features

### Secure Authentication

* Firebase Authentication
* Email and password login
* User-specific vault isolation

### Ephemeral Storage

* Automatic expiration
* Configurable self-destruct timer
* Firestore TTL integration
* Automatic cleanup process

### File Storage

* Upload files up to 5 MB
* Support for multiple file formats
* Secure Firebase Storage integration

### Secure Notes

* Store confidential text notes
* Time-based automatic deletion
* User-specific access controls

### Usage Limits

| Feature           | Limit      |
| ----------------- | ---------- |
| Maximum File Size | 5 MB       |
| Vault Capacity    | 50 MB      |
| Secret Lifetime   | 1–72 Hours |

### Modern User Interface

* Responsive design
* Tailwind CSS
* Mobile-friendly experience
* Lightweight frontend architecture

---

## Technology Stack

### Frontend

| Technology              | Purpose               |
| ----------------------- | --------------------- |
| HTML5                   | Application Structure |
| JavaScript (ES Modules) | Application Logic     |
| Tailwind CSS            | Styling               |
| Lucide Icons            | UI Icons              |

### Backend Services

| Service                 | Purpose              |
| ----------------------- | -------------------- |
| Firebase Authentication | User Authentication  |
| Firestore               | Metadata Storage     |
| Firebase Storage        | File Storage         |
| Firestore TTL           | Automatic Expiration |

---

## Architecture

```text
User
 │
 ▼
SecureVault Frontend
 │
 ├── Firebase Authentication
 ├── Firestore Database
 └── Firebase Storage
        │
        ▼
 Automatic TTL Cleanup
```

---

## Screenshots

Add screenshots of:

* Login Screen
* Dashboard
* Upload Secret
* Vault Items List
* Expiration Settings

Example:

```text
screenshots/
├── login.png
├── dashboard.png
├── upload-file.png
└── vault-items.png
```

---

## Project Structure

```text
secure-vault/
│
├── index.html
├── app.js
├── firebaseConfig.js
│
├── assets/
├── screenshots/
│
├── README.md
├── LICENSE
└── .gitignore
```

---

## Installation

### Clone Repository

```bash
git clone https://github.com/prakashgangurde-ux/Secure-Vault.git

cd Secure-Vault
```

### Configure Firebase

Create:

```javascript
// firebaseConfig.js

export const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

### Run Locally

Using Python:

```bash
python -m http.server 8000
```

Or:

```bash
npx serve .
```

Open:

```text
http://localhost:8000
```

---

## Firebase Security Rules

### Firestore

```javascript
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    match /artifacts/{appId}/users/{userId}/vault_items/{document} {
      allow read, write:
      if request.auth != null
      && request.auth.uid == userId;
    }
  }
}
```

### Firebase Storage

```javascript
rules_version = '2';

service firebase.storage {
  match /b/{bucket}/o {
    match /users/{userId}/{allPaths=**} {
      allow read:
      if request.auth != null
      && request.auth.uid == userId;

      allow write:
      if request.auth != null
      && request.auth.uid == userId
      && request.resource.size < 5 * 1024 * 1024;
    }
  }
}
```

---

## Firestore TTL Configuration

Enable automatic deletion:

1. Open Firebase Console
2. Navigate to Firestore Database
3. Open TTL Policies
4. Create Policy

```text
Collection Group: vault_items
Timestamp Field: ttlDate
```

Expired secrets will be removed automatically by Firebase.

---

## Security Highlights

* User-isolated storage
* Firebase Authentication
* Access-controlled Firestore rules
* Storage ownership validation
* Automatic secret expiration
* No shared public vaults
* Minimal data retention model

---

## Future Enhancements

* End-to-end encryption
* Password-protected secret sharing
* Secure shareable links
* Multi-factor authentication
* Download tracking
* Secure audit logs
* File preview support
* Progressive Web App (PWA)

---

## Deployment

SecureVault can be deployed on:

* Netlify
* Vercel
* Firebase Hosting
* GitHub Pages

Current deployment:

https://hawks-securevault-231125.netlify.app/

---

## Contributing

Contributions, issues, and feature requests are welcome.

Steps:

1. Fork the repository
2. Create a feature branch
3. Commit changes
4. Push changes
5. Open a pull request

---

## License

MIT License

See the LICENSE file for details.

---

## Author

Prakash Gangurde

GitHub:
https://github.com/prakashgangurde-ux

Building privacy-focused applications with modern web technologies.
