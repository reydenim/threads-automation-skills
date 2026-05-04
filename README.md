# Threads Automation Skills

![Threads Automation Skills banner](assets/threads-automation-banner.svg)

Reusable agent skills for Threads automation: contextual engagement, reply-back, visual post publishing, verification, and anti-AI writing polish.

## Language / Bahasa

- [English](#english)
- [Bahasa Indonesia](#bahasa-indonesia)

---

## English

### Skills included

- `threads-engagement` — Threads workflow for posting, liking, replying, reply-back, duplicate checks, language matching, and verification.
- `threads-engagement-aggressive` — **NEW v3** — Aggressive mode with CloakBrowser, contextual replies, language detection, persistent dedup, cache cleanup, health metrics, and post-run verification.
- `avoid-ai-writing` — writing polish skill to reduce generic/AI-ish captions and replies. Based on Conor Bronsdon's MIT-licensed `avoid-ai-writing` skill.

### Scripts

- `scripts/threads-engage-v3.js.example` — Full engagement script (Playwright + CloakBrowser). Configurable via environment variables.

### v3 Features

- 🧹 **Pre-run cache cleanup** — frees ~670MB, profile stays at ~8MB
- 🔒 **CloakBrowser stealth args** — 30+ flags for anti-detection
- 📊 **Browser health metrics** — page load times, JS heap, system RAM
- ⏱️ **Hard timeout watchdog** — 3 min max, prevents infinite hangs
- 📝 **Persistent dedup log** — no repeat replies within 6h cooldown
- 🎯 **Contextual replies** — extracts post text, detects language (ID/EN), identifies topic (10 categories), picks matching reply
- ✅ **Post-run /replies tab verification** — confirms replies actually posted (zero false positives)
- 🌍 **Language matching** — ID→ID, EN→EN, unknown→EN (global safe)

### Setup

```bash
# Environment variables (optional, has defaults)
export THREADS_PROFILE_DIR=~/.threads-profile
export THREADS_COOKIE_FILE=~/.hermes/browser-sessions/threads.json
export THREADS_EVIDENCE_DIR=~/.hermes/threads-evidence
export THREADS_BROWSER_BINARY=/path/to/cloakbrowser/chrome
export THREADS_USERNAME=your_username

# Install dependencies
npm install playwright

# Install CloakBrowser (anti-detect Chromium)
npm install -g cloakbrowser && cloakbrowser install

# Run
node scripts/threads-engage-v3.js.example
```

### Critical Fix (v3): Reply Submit Button

Threads reply modal has multiple elements with text "Reply" (reply icons from other posts). The actual submit button text is **"Post"** inside `[role="dialog"]`. Previous versions clicked the wrong button → silent failure.

---

## Bahasa Indonesia

### Skills yang tersedia

- `threads-engagement` — Workflow Threads untuk posting, like, reply, reply-back, cek duplikat, language matching, dan verifikasi.
- `threads-engagement-aggressive` — **BARU v3** — Mode agresif dengan CloakBrowser, reply kontekstual, deteksi bahasa, dedup persistent, cache cleanup, health metrics, dan verifikasi post-run.
- `avoid-ai-writing` — Skill untuk mengurangi caption/reply yang generik/terasa AI.

### Fitur v3

- 🧹 **Cache cleanup otomatis** — hemat ~670MB per run
- 🔒 **CloakBrowser stealth** — anti-detection dari Meta
- 📊 **Health metrics** — monitor performa browser
- ⏱️ **Watchdog 3 menit** — anti-hang
- 📝 **Dedup persistent** — nggak reply user yang sama dalam 6 jam
- 🎯 **Reply kontekstual** — baca isi post, deteksi bahasa & topik, pilih reply yang nyambung
- ✅ **Verifikasi di /replies tab** — konfirmasi reply beneran muncul di profil
- 🌍 **Language matching** — ID→ID, EN→EN, unknown→EN

---

## License

MIT
