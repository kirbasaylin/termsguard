# Terms Guard — Before You Sign Up

Terms Guard is a local-first Chrome extension that warns users about risky signup, trial, subscription, and checkout terms before they commit.

It scans the current page plus linked Terms, Privacy, Refund, Billing, and Cancellation pages, then surfaces issues like auto-renewal, refund limits, hard-to-cancel subscriptions, arbitration clauses, intro price jumps, data sharing, and hidden fees in plain English.

> Example: 5 things to check  
> Auto-renews after the trial · No refunds / all sales final · Must call to cancel · Class-action waiver · Price increases after intro period

## Why I Built This

People agree to contracts they do not have time to read. Terms Guard gives users a second set of eyes at the exact moment they are about to start a trial, subscribe, or pay.

## Features

- Detects signup, checkout, trial, subscription, billing, and payment pages
- Scans linked legal pages, not just the visible checkout page
- Flags concrete risks with source attribution
- Shows the exact sentence behind each warning
- Sets optional local cancel reminders before trial renewal
- Runs without a backend; page content is not uploaded or shared
## Tech Stack
- **Chrome Extension (Manifest V3)** — service worker architecture, no persistent background page.
- **Vanilla JavaScript (ES6+)** — no framework or build step; the extension runs the source files directly.
- **HTML5 / CSS3** — popup UI (`popup.html`, `popup.css`), no UI library.
- **Chrome Extension APIs** — `storage` (settings, reminders, doc cache), `alarms` (renewal reminders), `notifications` (browser alerts), and `host_permissions` for cross-origin fetching of linked legal pages.
- **Regex-based detection engine** — custom rule dictionaries with negation handling and specifics extraction (`detectors.js`).
- **No external dependencies / no network calls** — all page analysis happens locally in the browser.

## What it catches
Auto-renewal · free-trial-to-paid conversion · hard-to-cancel terms (call/mail/notice-period) · refund limits & windows · binding arbitration / class-action waivers · price jumps after an intro period · data sharing/selling · minimum commitments & early-termination fees · restocking/handling fees · final-sale & return-shipping conditions.

## Why this is different
- **Decision-moment, not a doc summarizer.** It only wakes up on signup/checkout pages and answers one question: *what should I know before I click pay?*
- **It reads the linked legal pages for you.** Most tools only see the page you're on. Terms Guard fetches and scans the Terms, Refund, and Cancellation pages too, then attributes each risk to its source.
- **Concrete, not fuzzy.** "Refund window is 48 hours" and "must call to cancel" are facts, not vibes.
- **Built-in Trial Trap.** Found a trial? One click sets a local reminder (with a browser notification a day before renewal) and saves the cancellation link. See all your reminders in the popup.
- **100% local.** Page text is analyzed in your browser. Nothing you visit is sent anywhere.

## Install (developer mode)
1. Unzip this folder.
2. Open `chrome://extensions/`, turn on **Developer mode**.
3. **Load unpacked** → select the `termsguard` folder.
4. Visit a trial/checkout page — the Terms Guard card slides up bottom-right.

## How it works
- `detectors.js` — the risk engine (regex dictionaries + negation handling + specifics extraction). Shared verbatim by the content script and the service worker.
- `content.js` — detects decision pages, scans the page, finds candidate legal links, asks the worker to fetch+scan them, renders the verdict card, and sets cancel reminders.
- `background.js` — fetches linked docs (cross-origin via `host_permissions`), strips HTML to text, scans, caches 24h, and runs the reminder alarms + notifications.
- `popup.*` — current-page verdict, a Reminders dashboard, and the on/off switch.

## Permissions, plainly
- `host_permissions: <all_urls>` — needed so the worker can fetch the Terms/Refund pages a checkout links to. (Required for the core feature; nothing is uploaded.)
- `alarms`, `notifications` — for cancel reminders. `storage` — settings, reminders, and a 24h doc cache.

## Tuning
All detection lives in the `RULES` array in `detectors.js`. Each rule has a category, severity, a regex, an optional `reject` (negation guard), and a `detail()` that extracts specifics (trial length, refund window, price-after-intro). Add patterns there. Risk thresholds and the verdict are in `aggregate()`.

## Roadmap
- Optional AI explainer ("why does this clause matter?") via an LLM API — the one piece that would need a key.
- A crowd database of "how to cancel X" per service.
- Locale packs (rules are English-only today).
- SPA-rendered terms pages (fetch currently reads static HTML).

## License
MIT.
