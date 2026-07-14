# GSD

A simple to-do app with three lists — Urgent, Soon, Future — that syncs between your devices in real time. No build step: it's a single HTML file that runs straight in the browser, backed by a free Firebase project for sync.

Tap the **GSD** chip any time to open Settings: dark mode, color themes, editable categories, and auto-move timing (e.g. a task sitting in Soon for a week automatically bumps to Urgent).

## 1. Create a Firebase project (free, ~5 minutes)

1. Go to [console.firebase.google.com](https://console.firebase.google.com) and sign in with any Google account.
2. Click **Add project**, give it any name (e.g. "gsd-todo"), and finish the wizard (you can skip Google Analytics).
3. In your new project, click the **</>** (web) icon to register a web app. Give it any nickname. You don't need Firebase Hosting for this step.
4. Copy the `firebaseConfig` object it shows you — you'll paste this into `index.html` in step 4.

## 2. Turn on Anonymous sign-in

1. In the left sidebar, go to **Build → Authentication**.
2. Click **Get started**, then open the **Sign-in method** tab.
3. Enable **Anonymous**.

This lets the app connect to your database without needing you to create a username/password — it just needs *some* signed-in session to satisfy the security rules below.

## 3. Create a Firestore database

1. In the left sidebar, go to **Build → Firestore Database**.
2. Click **Create database**, choose a location close to you, and start in **production mode**.
3. Once it's created, open the **Rules** tab and replace the contents with:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /todos/{syncCode} {
         allow get, write: if request.auth != null;
         allow list: if false;
       }
     }
   }
   ```

   This means: anyone with a signed-in session (which happens automatically when the app loads) can read or write a specific list *if they know its exact sync code* — but no one can browse or list all the codes in your database. It's the same security model as a shareable link: keep your code private and don't post it anywhere public.

## 4. Add your config to the app

Open `index.html` in this folder and find this block near the top of the `<script type="text/babel">` section:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID",
};
```

Replace it with the real values from step 1.

## 5. Put it on GitHub Pages

1. Create a new GitHub repository and push these files (`index.html`, `manifest.json`, `sw.js`, `icons/`, `README.md`) to it.
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, pick your main branch and the `/ (root)` folder, then save.
4. GitHub will give you a URL like `https://yourname.github.io/your-repo/`. That's your app.

## 6. Link your devices

1. Open the URL on your first device (e.g. your laptop). Click **Create a new code** — you'll get something like `K3F9-QX2P`.
2. Open the same URL on your other device (e.g. your phone). Type in that same code and hit **Connect**.
3. Both devices now show the same tasks, updating live.

You can view or change your sync code any time from Settings — tap the **GSD** chip at the top of the app.

## 7. Install it as an app

- **iPhone (Safari):** open the URL → Share icon → **Add to Home Screen**.
- **Android (Chrome):** open the URL → ⋮ menu → **Install app** (or **Add to Home screen**).
- **Laptop (Chrome/Edge):** look for the install icon in the address bar, or ⋮ menu → **Install GSD**.

## Notes

- Data lives in Firestore, not the device, so nothing is lost if you clear your browser.
- The free ("Spark") Firebase plan comfortably covers personal use like this.
- If you ever want a fresh, separate list, just create another code from the setup screen.
