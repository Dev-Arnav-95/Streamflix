# Streamflix-0

A private, single-user streaming website. One HTML file. No build tools, no npm, no framework.

> Built by [Dev-Arnav-95](https://github.com/Dev-Arnav-95)

---

## What it does

- Email/password login via Firebase — **no registration UI**, only accounts you manually create in Firebase Console can sign in
- Stays logged in across refreshes (Firebase session persistence)
- Browse trending movies and TV shows via TMDB
- Search across movies and series
- Filter movies by genre
- Watchlist and watch history — synced to Firestore, works across devices
- Two switchable stream servers
- TV season/episode picker
- Responsive — works on desktop, tablet, and mobile

---

## Setup (5 steps)

### 1. Get a TMDB API key
Create a free account at [themoviedb.org](https://www.themoviedb.org/), go to **Settings → API**, and copy your v3 key.

### 2. Set up Firebase
1. Go to [console.firebase.google.com](https://console.firebase.google.com) and create a project
2. Add a **Web App** to it — copy the config object it gives you
3. Enable **Authentication → Sign-in method → Email/Password**
4. Go to **Authentication → Users → Add user** — add your email and a strong password. This is the only account that will ever work.
5. Enable **Firestore Database → Create database**

### 3. Set Firestore security rules
In Firestore → Rules, paste this:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

### 4. Fill in `index.html`
Open `index.html` and find the `CONFIGURATION` block near the top of the `<script>` tag. Fill in:

```js
const APP_NAME = 'Your Site Name';   // shown in title, topbar, login

const FIREBASE_CONFIG = {
    apiKey:            'YOUR_FIREBASE_API_KEY',
    authDomain:        'YOUR_PROJECT_ID.firebaseapp.com',
    projectId:         'YOUR_PROJECT_ID',
    storageBucket:     'YOUR_PROJECT_ID.firebasestorage.app',
    messagingSenderId: 'YOUR_MESSAGING_SENDER_ID',
    appId:             'YOUR_APP_ID',
};

const TMDB_KEY = 'YOUR_TMDB_API_KEY';
```

That's it. Everything else works automatically.

### 5. Deploy
It's a single HTML file — put it anywhere:

- **GitHub Pages**: push to a repo, enable Pages in Settings → deploy from `main` branch root
- **Netlify / Vercel**: drag and drop the file
- **Any web server**: just upload it

---

## Customization

Everything you'd want to change is clearly marked in the code.

| Thing to change | Where |
|---|---|
| Site name | `APP_NAME` in the config block |
| Accent colour | `--accent` CSS variable at the top of `<style>` |
| Stream servers | `PLAYER_S1` / `PLAYER_S2` in the config block |
| Number of cards shown | `slice(0, 12)` in `buildHome()` |
| History limit | `state.user.history.length > 50` in `addToHistory()` |

---

## Tech

- Vanilla HTML/CSS/JS — no build step, no dependencies to install
- [Firebase](https://firebase.google.com) (Auth + Firestore) — free tier is more than enough
- [TMDB API](https://www.themoviedb.org/documentation/api) — free
- [Phosphor Icons](https://phosphoricons.com) — loaded from CDN
- Stream embeds from third-party servers (not affiliated)

---

## Notes on security

Firebase config keys in client-side code are normal and expected — they identify your project, not authenticate you. The actual security is enforced by Firestore rules (only your authenticated UID can read/write) and Firebase Auth (only accounts you create work).

If you want extra protection, go to Google Cloud Console → APIs & Services → Credentials → your API key → restrict it to your domain only.
