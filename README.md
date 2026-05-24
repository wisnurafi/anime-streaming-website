<h1 align="center">AniProject</h1>

![AniProject Website Logo](https://user-images.githubusercontent.com/69987890/177884319-0678f842-f3ca-4f62-8d31-7638ca954057.png)

An anime and manga streaming/reading website powered by the AniList, Consumet, and Aniwatch APIs. It provides information about released anime and manga, cast and staff details, episode streaming, manga reading, comments, watchlists, and more.

<p align="center">You can access this website on <a href='https://aniproject-dev.vercel.app/'>Vercel</a> or <a href='https://aniproject-website.onrender.com'>Render (slow cold start)</a>.</p>

> [!CAUTION]
> This project is strictly non-commercial. You may not generate revenue from it or include advertisements. Violating this policy may result in legal action.

## Navigation

- [Features](#sparkles-features)
- [Quick Deploy](#rocket-quick-deploy)
- [Roadmap](#pushpin-roadmap-unordered)
- [Tech Stack](#heavy_check_mark-tech-stack)
- [Getting Started](#computer-getting-started)
- [Firebase Database Structure](#books-firebase-database-structure)
  - [Authentication](#authentication)
  - [Collections and Documents](#collections-and-documents)
    - [Users](#users)
    - [Comments](#comments)
    - [Notifications](#notifications)
- [Screenshots](#camera-screenshots)

## :sparkles: Features

- [x] `Search`: Browse all anime and manga using filters.
- [x] `Watch`: Stream any available episode, dubbed or subbed.
- [x] `Read`: Read any available manga chapter.
- [x] `Comment`: Share thoughts on episodes and media pages.
- [x] `Sign In`: Log in with Google, AniList, or anonymously (with some restrictions).
- [x] `AniList Integration`: Use your AniList account to carry over settings, anime, and manga lists.
- [x] `Keep Watching`: Resume episodes from where you left off.
- [x] `Notifications`: Get notified when a new episode is released.
- [x] `Track Favorites`: Mark media as Completed, Dropped, Planning, and more.
- [x] `Episode Tracking`: Mark watched episodes and continue from there.
- [x] `News Feed`: Stay up to date with anime, manga, and industry news.

## :rocket: Quick Deploy

> [!IMPORTANT]
> You need to complete some configuration before the project will run properly. See the [Getting Started](#computer-getting-started) section, then provide those values to Vercel or any other host.

On Vercel: [![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2FErickLimaS%2Fanime-website&env=NEXT_PUBLIC_ANIWATCH_API_URL,NEXT_PUBLIC_CONSUMET_API_URL,NEXT_PUBLIC_ANILIST_CLIENT_ID,ANILIST_CLIENT_SECRET,NEXT_PUBLIC_NEXT_ROUTE_HANDLER_API,NEXT_PUBLIC_WEBSITE_ORIGIN_URL,NEXT_PUBLIC_FIREBASE_API_KEY,NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,NEXT_PUBLIC_FIREBASE_PROJECT_ID,NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET,NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER,NEXT_PUBLIC_FIREBASE_APP_ID,NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID,NEXT_PUBLIC_FIREBASE_DATABASE_URL)

## :pushpin: Roadmap (unordered)

- [ ] `Bug Fixes`
- [ ] `Hide Next Episode Images Until You Watch It (avoid spoilers)`
- [ ] `AniList Reviews/Comments`
- [ ] `AniList Threads`
- [ ] `New Media Sources`
- [ ] `New Video Player Features`
- [ ] `Profile Page`
- [ ] `Edit Media Progress Info`
- [ ] `Theme Selection`
- [ ] `Schedule Page/Section`

## :heavy_check_mark: Tech Stack

Front-end:

- `Next.js 14` (App Router)
- `TypeScript`
- `Redux Toolkit`
- `Axios` / `axios-retry`
- `Firebase` (Authentication)
- `GraphQL` (AniList API)
- `Framer Motion`
- `Swiper`
- `Vidstack` / `hls.js` (video player)
- `AniList API`, `Consumet API`, `Aniwatch API`

Back-end:

- `Firebase Firestore` (database)
- `Next.js Route Handlers` (server APIs)

## :computer: Getting Started

1. `Fork` (recommended) or `clone` this repository:

   ```bash
   git clone https://github.com/ErickLimaS/anime-website.git
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

3. Create a `.env.local` file in the project root (or copy `.env.example`) and fill in the values described below.

   - **External APIs** (host your own instance of each and save the URLs):
     - <a href='https://github.com/consumet/api.consumet.org' target="_blank" rel="noreferrer">Consumet API</a>
     - <a href='https://github.com/ghoshRitesh12/aniwatch-api' target="_blank" rel="noreferrer">Aniwatch API</a>

   - **AniList Login** (OAuth):
     - Sign in to your AniList account.
     - Go to the <a href='https://anilist.co/settings/developer'>Developer page</a> in Settings and click "Create New Client".
     - Add the name of your forked project/website and the redirect URL, then click "Save".
     - Copy the Client ID and Secret into your `.env.local`.
     - Tip: create two clients, one for development and one for production.

   - **Firebase** (for Google, Email, and Anonymous login plus the Firestore database):
     - Create a project on <a href='https://console.firebase.google.com/' target="_blank" rel="noreferrer">Firebase</a>.
     - All Firebase values for `.env.local` are available when you create the project.
     - **IMPORTANT**: Allow your hosted website domain in Firebase Authentication.
     - **IMPORTANT**: Update the rules in **Firestore Database**. Choose one of the following depending on the login methods you enable:
       - With **all** login methods (including AniList OAuth):

         ```javascript
         rules_version = '2';

         service cloud.firestore {
           match /databases/{database}/documents {

             match /{document=**} {
               // allows any read/write. Required because of AniList OAuth login.
               allow read, write: if true;
             }

           }
         }
         ```

       - With **only** Firebase login methods (no AniList login):

         ```javascript
         rules_version = '2';

         service cloud.firestore {
           match /databases/{database}/documents {

             match /users/{document=**} {
               // requires an authenticated user UID.
               allow read, write: if request.auth.uid != null;
             }

             match /comments/{document=**} {
               // public read; authenticated write.
               allow read: if true;
               allow write: if request.auth.uid != null;
             }

             match /notifications/{document=**} {
               // public read; authenticated write.
               allow read: if true;
               allow write: if request.auth.uid != null;
             }

           }
         }
         ```

   - Optional: this project ships a JSON file (~47 MB) with anime and manga data used as an offline database. The repo already contains it, but it may be outdated. To refresh it:
     - Visit <a href='https://github.com/manami-project/anime-offline-database' target="_blank" rel="noreferrer">anime-offline-database</a> and download the JSON file used only on the `Search` page.
     - Place the file in `/app/api/animes-database`, replacing the existing one.

   Example `.env.local`:

   ```bash
   # Consumet API
   NEXT_PUBLIC_CONSUMET_API_URL=https://your-hosted-consumet-api-url.com
   # Aniwatch API
   NEXT_PUBLIC_ANIWATCH_API_URL=https://your-hosted-aniwatch-api-url.com
   # AniList OAuth
   NEXT_PUBLIC_ANILIST_CLIENT_ID=your-anilist-client-id
   ANILIST_CLIENT_SECRET=your-anilist-secret
   # Next.js route handler. Append "/api/animes-database" to your hosted URL.
   NEXT_PUBLIC_NEXT_ROUTE_HANDLER_API=https://your-website.com/api/animes-database
   # Use localhost in development. Change this to your production URL when deploying.
   NEXT_PUBLIC_WEBSITE_ORIGIN_URL=http://localhost:3000
   # Firebase
   NEXT_PUBLIC_FIREBASE_API_KEY=your-firebase-value
   NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=your-firebase-value
   NEXT_PUBLIC_FIREBASE_PROJECT_ID=your-firebase-value
   NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=your-firebase-value
   NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER=your-firebase-value
   NEXT_PUBLIC_FIREBASE_APP_ID=your-firebase-value
   NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID=your-firebase-value
   NEXT_PUBLIC_FIREBASE_DATABASE_URL=your-firebase-value
   ```

4. Start the development server:

   ```bash
   npm run dev
   ```

5. Open `http://localhost:3000`. That's it.

## :books: Firebase Database Structure

### Authentication

Firebase Authentication is configured with four sign-in methods:

- Email
- Google
- AniList
- Anonymous
- <s>GitHub</s>

The user document stores things like:

- Profile photo
- Username
- Preferences (media source, adult content, subtitles, and more)
- Comments
- Notifications
- Bookmarked media
- Currently watching
- Episodes watched
- Chapters read

### Collections and Documents

The Firestore database has three collections:

#### Users

Holds a document per user, created after a successful sign-up.

#### Comments

Holds comments posted on episodes or media pages.

Comments are organized by AniList media ID. Each media document contains a sub-collection of all comments for that media, including comments posted on a specific episode.

This collection depends on the Users collection because every comment references its owner. Each comment also tracks interactions (likes and dislikes).

When a comment is posted, a small log entry is written to the user document with metadata about the interaction or the episode it was posted on.

#### Notifications

The Notifications collection holds a document per AniList media ID whenever a user subscribes to be notified about new episodes.

Each document has a sub-collection containing every user subscribed to that notification.

The document also tracks the episodes already delivered to users, the next one to be released, cover art, completion status, current status, and the last update date.

- User document relation: after a notification is delivered, the last episode info is written to the user document so the user can be notified again when the next episode is released.
