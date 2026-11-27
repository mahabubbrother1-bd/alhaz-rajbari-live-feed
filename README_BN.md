# ALHAZ RAJBARI LIVE — Full TV Broadcast System

A static GitHub Pages Control Studio + OBS Browser Source overlay, synchronized through Supabase Realtime private Broadcast channels.

## Features
- Separate Control Studio (`index.html`) and transparent OBS output (`overlay.html`)
- Email/password Control Studio authentication
- Private Realtime room; owner can publish, OBS viewer can receive
- One permanent OBS URL per room; changes are sent with **LIVE FEED-এ পাঠান**
- Professional logo upload, crop, resize, position, and hosted logo asset
- Lower-third, breaking-news bar, bottom ticker, speed/size, clock/date, LIVE/OFF AIR/REPLAY, location strip
- Desktop and mobile browser control

## 1. Create Supabase project
1. Create a project at https://supabase.com.
2. In Authentication → Providers, enable **Email**.
3. In Authentication → Sign In / Providers, enable **Anonymous Sign-Ins**. The OBS overlay uses an anonymous authenticated session to join a private read-only channel.
4. Open SQL Editor and run all of `supabase/schema.sql`.
5. In Realtime settings, keep private channel authorization enabled. For stricter deployment, turn **Allow public access** off; this app uses private channels.
6. Copy Project URL and the **Publishable key** from Project Settings → API Keys (or Connect). Never use a secret/service_role key in browser code.

## 2. Create the first Control Studio account
1. Open the deployed `index.html` page.
2. Enter Supabase Project URL and Publishable key in Setup / Config, then Save.
3. Enter your email and a strong password, click Create account. Verify email if your Supabase project requires it.
4. Log in.
5. Click **Create / Connect private room**. The app creates a high-entropy room topic tied to your signed-in user.
6. Copy the generated OBS Browser Source URL.

OBS URL-এ room topic, Supabase project URL এবং Publishable key স্বয়ংক্রিয়ভাবে যুক্ত থাকে; overlay.html হাতে edit করতে হবে না। Publishable key public client key—এটি secret/service_role key নয়।

## 3. Configure OBS
1. OBS → Sources → `+` → Browser.
2. Paste the copied `overlay.html?room=...` URL.
3. Set Width `1920`, Height `1080`, and optionally enable “Shutdown source when not visible” off if you want persistent connection.
4. Place the Browser Source ABOVE your camera/video/game sources.
5. The overlay is transparent; do not add an opaque background.
6. Keep the same URL. Open Control Studio in Chrome, sign in, edit graphics, and click **LIVE FEED-এ পাঠান**.

## 4. GitHub Pages deployment
1. Create a new GitHub repository, e.g. `alhaz-rajbari-live`.
2. Upload the project files and folders from this ZIP to the repository root. Keep `.github/workflows/deploy.yml` and `supabase/schema.sql`.
3. Set the default branch to `main`.
4. In GitHub repository → Settings → Pages, set Source to **GitHub Actions**.
5. Wait for the Deploy workflow to finish. Your site URL will be `https://YOUR-USERNAME.github.io/REPOSITORY/`.
6. Open that URL. Control Studio is at `/REPOSITORY/`; overlay file is `/REPOSITORY/overlay.html?room=...`.

## Security model / important notes
- The browser contains only the Supabase **Publishable** key. Never put `sb_secret_...` or `service_role` in HTML.
- Control Studio requires email/password authentication. Room creation is owner-bound by RLS.
- OBS uses Supabase anonymous sign-in and receives broadcasts only when it knows the unguessable room topic in its URL. Treat the OBS URL as a private bearer link: do not post/share it publicly. Regenerate the room if the URL leaks.
- The logo bucket is public-read so OBS can render the logo without exposing credentials; uploads are restricted to the authenticated user's own folder.
- GitHub Pages is public hosting. Do not put private passwords, service keys, personal data, or secrets in the repository.
- Supabase Realtime and Auth require an internet connection. GitHub Pages is static hosting and does not itself provide the realtime relay.
- This is a starter implementation. Test with a private/unlisted OBS scene before a public broadcast. For a production channel, consider adding rate limits, room rotation, account recovery, and stricter anonymous sign-in controls.

## Files
- `index.html` — Control Studio
- `overlay.html` — transparent OBS Browser Source
- `supabase/schema.sql` — tables, RLS, Realtime authorization, storage policies
- `.github/workflows/deploy.yml` — GitHub Pages deploy action

## Troubleshooting
- **OBS says OVERLAY CONFIG NOT SET**: Control Studio থেকে নতুন করে OBS URL copy করুন; URL-এ Supabase project URL ও Publishable key থাকা উচিত।
- **Room create denied**: confirm SQL ran successfully and you are logged into the same Supabase project.
- **OBS cannot connect**: enable Anonymous Sign-Ins, confirm the overlay URL has the full `?room=` query, and confirm Realtime is enabled.
- **No logo**: verify Storage bucket `broadcast-assets` exists and public read policy was created.
- **Control Studio opens but doesn't update OBS**: make sure both use the same room URL, the channel is private, and you clicked the publish button after editing.
