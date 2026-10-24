# RO Visit MoM: Firebase setup

About 15 minutes, one time. Use the same Google account you will sign in to the app with.

## 1. Create the Firebase project
1. Open https://console.firebase.google.com and click **Create a project**.
2. Name it, for example `ro-visit-mom`. Turn **Google Analytics off**. Click **Create project**.

## 2. Switch to the Blaze plan (needed for photo and PDF storage)
1. Bottom-left **Spark** → **Upgrade** → **Blaze** and link a billing account (card).
2. Set a **budget alert** (for example ₹200 a month) when asked. One person's MoMs stay well inside the free allowance, so the bill should normally be ₹0.

## 3. Register the web app and copy the config
1. Project overview → click the **</>** (Web) icon. Nickname: `RO MoM`. Do **not** tick Firebase Hosting. Click **Register app**.
2. Copy the `firebaseConfig = { ... }` block that appears. You will paste it into the app in step 8.
   (Later you can find it again in ⚙ Project settings → General → Your apps.)

## 4. Turn on Google sign-in
1. **Build → Authentication → Get started**.
2. **Sign-in method** tab → **Google** → Enable → choose your support email → **Save**.
3. **Settings** tab → **Authorized domains** → **Add domain** → `pro-bhupesh.github.io`.

## 5. Create the database (Firestore)
1. **Build → Firestore Database → Create database**.
2. Location: **asia-south1 (Mumbai)**. Start in **production mode**.
3. Open the **Rules** tab, delete what is there, paste the contents of `firebase/firestore.rules`.
4. Replace `YOUR_EMAIL@gmail.com` with your Google account email. Click **Publish**.

## 6. Create file storage
1. **Build → Storage → Get started**. Location: **asia-south1**. Production mode.
2. **Rules** tab: paste `firebase/storage.rules`, replace `YOUR_EMAIL@gmail.com` with your email, **Publish**.

## 7. (Optional) Let "Open" reload photos of old MoMs
Without this, reopening an old MoM brings back everything except its photos (the saved PDF always has them).
1. In the Google Cloud console for the same project, click the **Cloud Shell** icon (>_ at the top right).
2. Run these two lines, replacing `YOUR_BUCKET` with the storageBucket value from your config (for example `ro-visit-mom.firebasestorage.app`):
   ```
   echo '[{"origin":["https://pro-bhupesh.github.io"],"method":["GET"],"maxAgeSeconds":3600}]' > cors.json
   gcloud storage buckets update gs://YOUR_BUCKET --cors-file=cors.json
   ```

## 8. Publish the app and sign in
1. Upload `index.html`, `sw.js`, `manifest.webmanifest` and the `icons` folder to the MOM_Maker- GitHub repo.
   **Do not upload `ro_master_data.json`.** It holds the RO list and BRT sales data, which now stay private in Firebase.
2. Open https://pro-bhupesh.github.io/MOM_Maker-/ on your phone.
3. Paste the `firebaseConfig` block from step 3 → **Save and continue**.
4. **Sign in with Google**.
5. When asked, choose `ro_master_data.json`. It is stored in your database, so you only do this once.
6. Install the app from the **⬇ Install app** button.

## Every month (new BRT)
Send the new BRT file to Claude for a new `ro_master_data.json`, then in the app tap your initial (top right) → **Update RO / BRT data file**. No GitHub upload needed.

## Where things are kept
| What | Where |
|---|---|
| MoM details, checklist, points, signatures | Firestore: `users/{your id}/moms/{MoM no.}` |
| PDF and photos | Storage: `users/{your id}/moms/{MoM no.}/` |
| RO list, BRT sales, map data | Firestore: `config/master` |
| Current draft | This phone only (until you download or share the PDF) |
