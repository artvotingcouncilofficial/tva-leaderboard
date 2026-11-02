# TVA Player Management Website — No Storage Edition

Static HTML website using Firebase Authentication and Cloud Firestore only.

## Included functionality
- Email/password sign-in with ADMIN and VIEWER roles
- Player management (ADMIN) and read-only viewing (VIEWER)
- Match score entry with validation and duplicate-entry protection
- Live dashboard and leaderboard sorting
- Generate PNG: creates leaderboard images locally in the browser and downloads them directly

## Removed functionality
- Firebase Storage integration and storage bucket configuration
- Generate Report button and saved report history
- Firestore `reports` collection usage

PNG generation does not upload or save anything to Firebase.

## 1. Firebase setup
1. Create a Firebase project and register a Web app.
2. Copy the Web app config into `public/firebase-config.js`. The config requires `apiKey`, `authDomain`, `projectId`, `messagingSenderId`, and `appId`. There is no `storageBucket` field.
3. Authentication → Sign-in method → enable Email/Password.
4. Create a Firestore database.
5. Publish `firestore.rules` from this project in Firestore → Rules.

## 2. Create the first admin account
1. Authentication → Users → Add user.
2. Copy the new user's UID.
3. In Firestore, create collection `users` and a document whose ID is exactly that UID.
4. Add `email` (string), `role` (string) = `ADMIN`, and optional `displayName` (string).
5. For a viewer, create an Authentication user and a matching `users/{uid}` document with `role` = `VIEWER`.

## 3. Host on GitHub Pages
Upload the project contents to your repository. In repository Settings → Pages, select Deploy from a branch, branch `main`, folder `/public`. The published site URL will be shown by GitHub.

## 4. Optional Firebase Hosting
Install Node.js LTS and Firebase CLI, then run from this project directory:

```bash
npm install -g firebase-tools
firebase login
firebase use --add
firebase deploy --only hosting,firestore:rules
```

## Security notes
- Firebase web config is client-side project identification, not a service-account secret. Never upload service-account JSON/private keys.
- Firestore rules restrict player and match writes to ADMIN users.
- Player deletion is implemented as deactivation to preserve match history.
- Score validation is performed in the browser and reinforced by Firestore rules.

## Firestore collections
- `users/{uid}`: email, displayName, role
- `players/{playerId}`: playerName, instagramUsername, profileImage, isActive
- `matchEntries/{entryId}`: playerId, matchId, matchName, matchDate, matchesPlayed, votes, wins, draws, losses, score, remarks
