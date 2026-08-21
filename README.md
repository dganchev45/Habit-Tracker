# Habit Tracker

A minimalist, dark-mode habit tracker built to run as an installable iOS PWA. Everything — logic, UI, and styling — lives in a single `index.html` (React + Tailwind, loaded from CDN, no build step). All data is stored locally in your browser's `localStorage`; there is no backend and nothing is ever sent to a server.

## Running it

You can open `index.html` directly in a browser, but serving it over HTTP is recommended (required for the service worker and cleanest for testing installability):

```
python3 -m http.server 8080
```

Then visit `http://localhost:8080`.

## Installing on iOS

1. Open the app in Safari on your iPhone (over your local network, e.g. `http://<your-computer-ip>:8080`, or any HTTPS host once deployed).
2. Tap the Share icon, then **Add to Home Screen**.
3. Launch it from the home screen — it opens full-screen with no browser chrome, its own icon, and works offline after the first load (thanks to `sw.js`, which caches the app shell).

**Important:** an installed home-screen app has its own separate `localStorage` from Safari itself — data does not carry over between the two. Export a backup (Settings → Export Data) before installing, and again before switching devices or browsers.

## What's in this repo

| File | Purpose |
|---|---|
| `index.html` | The entire application: data model, streak/XP/badge engine, and all React UI, in one file. |
| `manifest.webmanifest` | PWA install metadata (name, icons, standalone display mode, theme colors). |
| `sw.js` | A small cache-first service worker so the app still opens without a network connection. |
| `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` | App icons for home-screen installation. |

`index.html` alone is a fully functional app — the other files exist only to make "Add to Home Screen" produce a real-looking, installable, offline-capable app on iOS.

## Data model

Everything is stored under a single `localStorage` key, `habitTracker.state`, as one JSON object: habit definitions, a date-keyed log of completions/values, and derived gamification state (XP, level, unlocked badges). Streaks, completion rates, and the heatmap are all computed on the fly from the raw logs rather than stored — so importing an old backup, or editing history, always produces consistent numbers.

## Features

- **Habit types:** simple yes/no habits, or measurable habits with a target, unit, and quick +/- logging.
- **Scheduling:** every day, specific days of the week, or N times per week.
- **Streaks & stats:** current/longest streaks (schedule-aware), a 6-month GitHub-style heatmap, weekly completion bars, and a 90-day completion rate.
- **Gamification:** XP for logging habits, hitting perfect days, and crossing streak milestones; 10 named levels from Novice to Master of Routine; 12 unlockable achievement badges.
- **Settings:** export your data as JSON (download or copy), import a backup, or reset to a blank slate or the sample habits.
- **Sample data:** the app seeds itself with ~7 sample habits and 28 days of realistic history on first launch, so streaks, the heatmap, and a few badges are already populated.

## Known limitations of this environment

This app was built in a sandboxed environment without a real browser or access to the CDN hosts it depends on at runtime (unpkg, Tailwind's CDN). Its logic was verified with a Node-based test harness instead of by clicking around. Before considering it fully done, please verify manually:

- Open it in Chrome DevTools → Application tab and confirm the Manifest and Service Worker show up cleanly with no errors.
- Check responsive layout at common iPhone widths (375, 390, 430px) and in landscape.
- Toggle "Offline" in DevTools and reload — the app should still open.
- Export data, then re-import it, and confirm stats match.
- Actually install it on an iPhone via Safari → Share → Add to Home Screen, and confirm it launches full-screen with the right icon.
