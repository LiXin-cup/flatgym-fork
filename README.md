# Flatgym

[![CI](https://github.com/zerotonin/flatgym/actions/workflows/ci.yml/badge.svg)](https://github.com/zerotonin/flatgym/actions/workflows/ci.yml)
[![Live demo](https://img.shields.io/badge/demo-live-brightgreen)](https://zerotonin.github.io/flatgym/)
[![License: GPL-3.0-or-later](https://img.shields.io/badge/License-GPL--3.0--or--later-blue.svg)](LICENSE)
[![Tests: Playwright](https://img.shields.io/badge/tests-Playwright-45ba4b)](tests/flatgym.spec.js)
[![Runtime dependencies: 0](https://img.shields.io/badge/runtime%20deps-0-success)](gym-tracker.html)
[![No tracking](https://img.shields.io/badge/tracking-none-success)](#principles)

A one-file, offline-first gym logger. No account, no server, no tracking. Open the HTML file in any browser — that's the whole app.

## Why

Most workout trackers want an account, a subscription, a location permission, or all three. Flatgym is the opposite: a single HTML file, your data stays in your browser's `localStorage`, CSV export when you want a backup.

## Install

**You need exactly one file:** [`gym-tracker.html`](gym-tracker.html).

No npm, no pip, no Docker, no runtime, no build step. Just the HTML. Everything else in this repo (`tests/`, `package.json`, `.github/`) is dev-time tooling — not needed to run the app.

1. Download [`gym-tracker.html`](gym-tracker.html) ([raw link](https://raw.githubusercontent.com/zerotonin/flatgym/main/gym-tracker.html)).
2. Open it in any modern browser — desktop or mobile.
3. Log your sets. Export CSV any time you want a backup.

### Live demo

The latest HTML is served at [https://zerotonin.github.io/flatgym/](https://zerotonin.github.io/flatgym/) once CI is green on `main`. Open it on your phone once to try; download the file once you're sold.

### On your phone

- **Android**: three options. (1) Open [the live demo](https://zerotonin.github.io/flatgym/) in Chrome and "Add to Home Screen" — installs as a PWA, fully offline after first visit. (2) Sideload the APK from any GitHub release (or build it yourself from [`android/`](android/)). (3) Wait for the F-Droid listing once v0.15 lands there.
- **iOS**: Safari restricts `file://` storage; use the live demo URL and "Add to Home Screen" — also installs as a PWA. A native iOS build is intentionally out of scope for now.

## What it does

- **Logging.** Four default presets (Push / Pull / Legs / Accessory) you can fully rename, re-emoji, and reorder. Per-set weight + reps with a ± toggle for assisted lifts (negative weights). A cardio block per workout — steady or interval, with HIIT/Tabata quick-fill. Free-text **workout notes** at the bottom of every session. Autosave draft survives tab close. Metric / Imperial toggle in Settings.
- **Rest timer.** Auto-starts when you add a new set (default 120 s, configurable in Settings; on/off toggle). Floating pill at the bottom of the workout view counts down; skip button to dismiss; gentle beep + vibration when done.
- **Workout duration.** Auto-tracked from the first set logged to the moment you tap Save. History shows it as a small ⏱ chip next to the date and preset.
- **Exercises.** Name autocomplete from your own history plus a 873-entry library bundled inline ([yuhonas/free-exercise-db](https://github.com/yuhonas/free-exercise-db), public domain). The `?` icon next to a library-matched exercise opens a reference widget with images and instructions in a new tab. Add custom exercises at will. Swap in alternatives mid-workout (`🔄`) or push an exercise to another day (`⏭ Shift`).
- **History.** Chronological list of past workouts. Each entry has a `⋯` menu: **Edit** (reuses the log screen, so a typo fix is one tap), **Duplicate to today**, **Delete**, **Share**. Edits never clobber an in-progress draft.
- **Stats.** A month calendar at the top (Mon-first columns; today framed in accent; preset emoji instead of the day number on workout dates; greyed emoji + `+N` badge for double-session days; chevrons to walk back through prior months). Below that: per-exercise progression chart, all-time top set + estimated 1RM (Epley) + best session volume, workout frequency for 7d / 30d / all-time. Weight is treated as a signed effective-load axis so a chin-up journey from `−75 → −30 → 0 → +10 → +25` reads as monotonic progress.
- **Body.** A fourth tab (📏) for free-form measurements — bodyweight, waist, biceps, body-fat %, anything in any unit. Each metric becomes a card with latest value, delta arrow, sparkline, and full history.
- **Share.** Any past workout exports as a 1080×1080 PNG card with PR badges, totals, and a `github.com/zerotonin/flatgym` wordmark. Web Share API on mobile, clipboard / download on desktop, plain-text alternative for chat apps that don't take images.
- **Backup.** CSV export/import covers everything (workouts and body measurements). Round-trip lossless. Backward-compatible with every earlier export format. A configurable **backup reminder** (default: weekly on Sunday) shows a banner on home when you're overdue, and on phones the **📤 Share CSV** button hands the file to the OS share sheet — Drive, Files, email, anything you've got installed. No Drive SDK, no OAuth, no cloud permissions on our side.

For a per-version changelog, see [Releases](https://github.com/zerotonin/flatgym/releases).

## Documentation

- [`gym-tracker.html`](gym-tracker.html) — the whole app.
- [`manifest.webmanifest`](manifest.webmanifest), [`sw.js`](sw.js), [`icons/`](icons) — PWA polish (installable, offline-first).
- [`android/`](android) — thin native WebView wrapper used for the F-Droid build. The HTML lives only at the root; the Android project bundles it into the APK at build time.
- [`LICENSE`](LICENSE) — GPL-3.0-or-later, full canonical text.
- [`tests/flatgym.spec.js`](tests/flatgym.spec.js) — Playwright E2E suite.
- [`playwright.config.js`](playwright.config.js) — test runner config; Python's `http.server` serves the HTML during tests.
- [`.github/workflows/ci.yml`](.github/workflows/ci.yml) — CI/CD: runs tests on every push/PR and deploys the HTML to GitHub Pages on green `main`.

## Roadmap

| Version | Theme |
| --- | --- |
| v1.0 | PWA polish (manifest, service worker, "Add to Home Screen") |

Past releases are listed under [Releases](https://github.com/zerotonin/flatgym/releases).

## Exercise data source

The autocomplete library and the `?` widget pull from [yuhonas/free-exercise-db](https://github.com/yuhonas/free-exercise-db) — 873 exercises, Unlicense (public domain). Exercise *names* are bundled inline (~40 KB); the richer data (instructions, muscles, equipment, two demo images per exercise) is fetched on the first `?` click and cached in `localStorage` so subsequent lookups are instant and fully offline.

Why this one and not the bigger databases:

- **wger** (wger.de): has per-exercise permalinks but its data is CC-BY-SA 3.0 / 4.0. Share-alike adds friction for bundling, and wger's own code is AGPL-3.0.
- **ExerciseDB API** (exercisedb.dev): 11 000+ exercises with gifs/videos, but the project is **AGPL-3.0** and the dataset is only available over a network API — not bundle-friendly.
- **wrkout/exercises.json**: same data as free-exercise-db (parent fork).
- **exercemus**: curated cross-reference of wger + wrkout, inherits their licences.

**AGPL** (GNU Affero General Public License) is GPL's network-use sibling: if you modify AGPL code and let other people use the modified version *over a network* (e.g. as a web service), you must publish the source. Plain GPL's obligation only triggers on binary distribution, so running GPL code behind a web UI without sharing modifications is allowed; AGPL closes that "SaaS loophole." For a bundled single-file app Flatgym doesn't *have* to avoid AGPL data, but AGPL is copyleft-viral onto anyone who forks Flatgym and modifies it, which is a bigger commitment than we need to push onto contributors. Unlicense has no such obligation.

## Security

Flatgym is a single-file offline app, so the threat surface is small — no server, no auth, no shared state, no CSRF. The realistic attack vectors that could affect a single user are: **(a)** XSS payloads in user-typed data (exercise names, preset labels, body-measurement names, etc.) that get rendered into the page, and **(b)** malicious cells in an imported CSV that cause formula execution when the user re-shares the CSV in Excel/Sheets.

What's been audited and hardened:

| Area | Status | Notes |
|---|---|---|
| **HTML injection / stored XSS** via `innerHTML` template literals | escaped | Every interpolation of user-typed data (exercise names, preset labels/emojis, body names/units, alternative-of strings, modal titles, attribute values in the preset editor) is wrapped in `escHTML()`. A malicious CSV-imported exercise name like `<img src=x onerror=…>` renders as text. |
| **CSV formula injection** on export (OWASP "CSV Injection") | mitigated | Cells starting with `=`, `+`, `-`, `@`, or tab are prefixed with a single apostrophe so spreadsheet software treats them as text instead of formulas. Round-trip through `fromCSV` strips the apostrophe again, so re-importing a previously-exported CSV is lossless. |
| **CSV quote escaping** (RFC 4180) | proper | Cells with embedded `"` are doubled (`""`); the parser handles that on import. |
| **Prototype pollution** via parsed JSON / CSV | not exploitable | `JSON.parse` treats `__proto__` as an own-property since ES2017+; no `Object.assign(target, parsedSrc)` patterns are used; key-by-key assignment from CSV doesn't trigger prototype changes. |
| **Open redirects / URL injection** | n/a | No URL parameters are parsed; every `window.open(...)` URL is either a constant or built from the bundled `EX_LIB_ID` array (not user-typed). |
| **Clickjacking / iframes** | n/a | The app doesn't load any external iframes and isn't designed to be embedded. |
| **`eval`, `Function`, `setTimeout(string, …)`, `javascript:` URIs** | none | None used. |
| **`target="_blank"` reverse-tabnabbing** | mitigated | All `_blank` opens use `rel="noopener"`. |
| **Service worker** | n/a | Not yet added (planned for v1.0 PWA polish; will be added with a versioned cache name and kill-switch). |

### Threat model — what we *don't* defend against

- **Self-attack via DevTools / direct localStorage edit.** The user is the only actor on their device; modifying `localStorage` to corrupt their own state is out of scope.
- **Lost-or-stolen device.** Your gym data is in the device's browser storage. If your unlocked phone is in someone else's hands, they can read it. Use device-level lock screens and app-level OS protections if that matters to you.
- **Malicious browser extensions.** A browser extension that runs in the same origin can read everything; that's a property of the browser, not the app.

## Principles

- No login, no account, no backend.
- No ads, no analytics, no tracking.
- No gamification, no social features.
- CSV is the only data-transfer mechanism.

## Disclaimer

Flatgym is a logging tool, not training advice. The app records what you tell it to record; it does not prescribe weights, vet form, or judge whether what you're about to lift is safe for you. Strength training carries injury risk. Consult a medical professional before starting an exercise programme, get coached on form, scale loads to your own ability, and don't push through pain. The software is provided "as is" with no warranty (see [LICENSE](LICENSE) §15-17).

## Licence

Copyright © 2024–2026 Bart Geurten (zerotonin).

Released under [GPL-3.0-or-later](LICENSE). Anyone can fork Flatgym, but any distributed version must also be open source and GPL-3 — so no one can wrap it in ads, lock it behind a subscription, and ship it closed.

## Development

The app has zero runtime deps, but the test suite uses Playwright.

```bash
npm install
npx playwright install chromium   # one-time browser download
npm test                          # headless
npm run test:ui                   # Playwright UI mode
```

CI runs on every push and PR via [GitHub Actions](.github/workflows/ci.yml):

- Playwright E2E tests on Chromium.
- On green `main`, deploys `gym-tracker.html` to GitHub Pages.

## Contributing

Issues and PRs welcome. Please keep the single-file ethos — no build step, no frameworks, no runtime dependencies in the app itself.

---

<div align="center">
  <img src="flatgym.png" alt="Flatgym logo" width="160">
</div>
