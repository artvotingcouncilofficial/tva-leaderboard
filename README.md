# TVA Player Management Website — Firebase Edition

This is a static website using the Firebase modular Web SDK:
- Firebase Authentication (email/password)
- Cloud Firestore (players, matchEntries, reports, users)
- Firebase Storage (generated leaderboard PNG pages)
- Firebase Hosting (website deployment)

## 1. Create and configure Firebase

1. Open https://console.firebase.google.com and create a project.
2. Add a **Web app** in Project settings.
3. Copy its Firebase config into `public/firebase-config.js`.
4. Authentication → Sign-in method → enable **Email/Password**.
5. Firestore Database → Create database.
6. Storage → Get started.
7. In Project settings, ensure your Hosting project is the same Firebase project.

## 2. Create the first admin account

1. Authentication → Users → Add user. Create the admin email/password.
2. Copy the new user's UID.
3. Firestore → create collection `users`.
4. Create a document whose document ID is exactly that UID.
5. Add fields:
   - `email` (string)
   - `role` (string) = `ADMIN`
   - `displayName` (string), optional
6. Do not let the browser choose or set its own ADMIN role.

To create a viewer account, create the account in Authentication, then create
`users/{that-uid}` with `role: "VIEWER"` in Firestore.

## 3. Install Firebase CLI

Install Node.js LTS, then run:

```bash
npm install -g firebase-tools
firebase login
```

## 4. Deploy

Open a terminal in this project folder:

```bash
firebase use --add
```

Select your Firebase project. Then deploy:

```bash
firebase deploy
```

The CLI deploys Hosting and the Firestore/Storage rules. If you prefer to deploy
only the website first:

```bash
firebase deploy --only hosting
```

The website URL will be shown by the CLI, typically:
`https://YOUR-PROJECT-ID.web.app`

## 5. Local testing

You can run:

```bash
firebase emulators:start
```

For emulator use, configure the SDK to connect to the emulators before testing.
For a simple hosted preview, deploy to Firebase Hosting.

## Important security notes

- The Firebase web config is client-side project identification, not a server secret.
- Never put service-account JSON/private keys in this website.
- Firestore and Storage rules are security boundaries. Review them before production.
- The app expects a `users/{uid}` document for every signed-in user.
- Admin role is assigned manually in Firestore by the project owner.
- Viewers can read app data but cannot change players, match entries, or reports.
- Player deletion is implemented as deactivation so match history remains.
- This starter performs score validation in the browser and rules. For stronger
  protection against concurrent updates or privileged workflows, move score
  creation/report generation to trusted Cloud Functions and add App Check.

## Data model

- `users/{uid}`: `email`, `displayName`, `role`
- `players/{playerId}`: `playerName`, `instagramUsername`, `profileImage`, `isActive`
- `matchEntries/{entryId}`: `playerId`, `matchId`, `matchName`, `matchDate`,
  `matchesPlayed`, `votes`, `wins`, `draws`, `losses`, `score`, `remarks`
- `reports/{reportId}`: `reportName`, `generatedAt`, `totalPlayers`,
  `totalPages`, `imageUrls`, `firstImage`, `generatedBy`

Player aggregate statistics are calculated from matchEntries in the browser
when the live Firestore snapshots update.
