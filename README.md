# BioGuard

A personal health records vault. Upload medical documents, keep them organized in folders, and have Gemini read them for you: each document gets a plain-language summary, a suggested category, a risk level and a list of abnormal values. A chat assistant answers general health questions, and a whole folder can be shared with a doctor through a link that expires after 24 hours.

## Features

- **Document vault.** Upload images and PDFs into folders. Files are stored per user in Firebase Storage, and storage rules only let you read and write your own files.
- **AI document analysis.** Gemini reads a lab report, prescription or scan and returns structured JSON: a 2-3 sentence summary, a category (lab report, prescription, imaging, insurance, other), a risk level and any anomalies it found. It tries a list of Gemini models in order and falls back if one fails. Each user gets 5 analyses a day, tracked in Firestore.
- **Health chat assistant.** A Gemini chat with conversation history, rate-limited to 5 messages a minute per IP.
- **Shareable records.** Bundle a folder into a zip and share it through a short link that expires after 24 hours. The share page checks the expiry before allowing a download.
- **Nearby doctors.** A Google Maps view for finding doctors and clinics near you.
- **Health news.** Top health headlines from NewsAPI, cached for an hour, with sample articles as a fallback.

## Stack

- **App:** Next.js 14 (App Router, server actions), React 18, TypeScript, Tailwind CSS, Radix UI, Framer Motion
- **Backend services:** Firebase Auth, Firestore, Firebase Storage, Firebase Admin SDK
- **AI and APIs:** Google Gemini, Google Maps Places, NewsAPI

## Running it

You need Node.js 18+, a Firebase project (Auth, Firestore and Storage turned on) and a Gemini API key.

```bash
npm install
cp .env.example .env.local
# fill in the Firebase config, FIREBASE_SERVICE_ACCOUNT_KEY and GEMINI_API_KEY
npm run dev
```

Then open http://localhost:3000. `NEWS_API_KEY` and `NEXT_PUBLIC_GOOGLE_MAPS_API_KEY` are optional; without them the news feed shows sample articles and the map stays off. Deploy `storage.rules` to your Firebase project so each user can only reach their own files.

## Project layout

```
src/
  app/
    actions/      server actions: document analysis, chat, news
    dashboard/    documents, appointments, news, profile
    share/[id]/   public page for expiring shared folders
    auth/         login and signup
  components/     dashboard, AI assistant, document modals, landing page
  lib/            Firebase Admin setup, folder sharing (zip bundles)
  hooks/          document listing from Firebase Storage
storage.rules     per-user access rules
```
