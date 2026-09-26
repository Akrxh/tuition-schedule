# Tuition Schedule — Manga Edition

A single-page tuition/class schedule tracker with a manga-comic UI, backed by
Firebase Firestore for real-time sync across devices.

## Files

- `index.html` — the entire app (HTML, CSS, JS in one file)
- `data.json` — blank starter data shape (for reference only; the app itself
  reads/writes Firestore, not this file)

## Data model

Each document in the `classes` collection:

| Field            | Type            | Meaning                                             |
|------------------|-----------------|------------------------------------------------------|
| `subject`        | string          | Class name / subject                                 |
| `day`            | string          | Recurring weekday (e.g. `"Monday"`)                  |
| `start`, `end`   | string (`HH:MM`)| Class time window                                     |
| `mode`           | `"online"` \| `"offline"` | Current delivery mode                       |
| `postponed`      | boolean         | Whether this class is currently postponed             |
| `postponedDate`  | string (`YYYY-MM-DD`) or `null` | The specific date it's postponed to |
| `cancelledDates` | array of `YYYY-MM-DD` strings | Dates this class instance was cancelled |

The class is still a *recurring weekly* entry (`day`), and `postponedDate` /
`cancelledDates` only affect specific single occurrences — the recurring
schedule itself is never deleted by a postponement or cancellation.

## Features

- **Live sync** — Firestore's `onSnapshot` listener keeps every open tab in
  sync instantly; no manual refresh needed.
- **Connection status pill** — next to the logo, shows **Connecting...**
  (yellow), **Online** (green), or **Offline** (red).
- **Today / Tomorrow view** — the home tab lists what's happening today and
  tomorrow, computed from the real calendar date (not just the weekly
  pattern), so postponements and cancellations are reflected correctly.
- **Live "Attending" tag** — during a class's actual time window, it's
  tagged and surfaced at the top of Today's view. When nothing is running,
  it shows "There is no class now."
- **Week view** — full 7-day schedule, editable, with add/edit/delete.
- **Online/Offline toggle** — one tap flips a class's delivery mode.
- **Postpone to a specific date** — pick any date via a date input; can be
  undone.
- **Cancel one or more today's classes** — checkboxes on Today's cards plus
  a "Cancel Selected" button; cancels only that day's occurrence, not the
  whole recurring class.

## Notes / limitations

- There's no authentication — anyone with the Firebase config (visible in
  the page source) can read/write the `classes` collection unless you add
  Firestore security rules restricting access.
- The app relies on Firestore's default network behavior for
  connectivity; it does not yet implement local offline caching (e.g.
  `enableIndexedDbPersistence`), so writes made while fully offline will
  fail until reconnected rather than queue automatically.
  
