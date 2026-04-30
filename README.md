# Threads Automation Skills

Reusable agent skills for Threads automation: contextual engagement, reply-back, visual post publishing, verification, and anti-AI writing polish.

## Language / Bahasa

- [English](#english)
- [Bahasa Indonesia](#bahasa-indonesia)

---

## English

### Skills included

- `threads-engagement` — Threads workflow for posting, liking, replying, reply-back, duplicate checks, language matching, and verification.
- `avoid-ai-writing` — writing polish skill to reduce generic/AI-ish captions and replies. Based on Conor Bronsdon's MIT-licensed `avoid-ai-writing` skill.

### Install

```bash
npx skills add reydenim/threads-automation-skills --all
```

Or install manually by copying the skill folders into your agent skills directory.

### What it helps agents do

- Restore/check Threads login session from local cookies.
- Create Threads posts with image + caption.
- Reply contextually to activity/notifications.
- Match the target post language.
- Avoid generic AI-sounding replies.
- Check duplicates before replying.
- Verify posted replies via the profile Replies tab.
- Produce clean reports with `VERIFIED`, `FAILED`, or `UNVERIFIED` status.

### Required setup

1. Replace `@YOUR_USERNAME` in your prompts/cronjobs with your Threads username.
2. Export your own Threads cookies/session using Cookie-Editor or a similar extension.
3. Save your cookies locally, for example:

```text
~/.hermes/browser-sessions/threads.json
```

Do **not** commit or share your cookie/session file.

### Example cron prompt

```text
Every 45 minutes, perform balanced Threads engagement for @YOUR_USERNAME using the saved browser session at ~/.hermes/browser-sessions/threads.json.

- Verify login first.
- Scan candidate posts or activity notifications.
- Like up to 5 posts per run.
- Reply at most once per run.
- Match target post language.
- Apply avoid-ai-writing pass before posting.
- Duplicate-check before replying.
- Verify replies via https://www.threads.com/@YOUR_USERNAME/replies before reporting success.
- Report VERIFIED / FAILED / UNVERIFIED honestly.
```

### Security notes

Never share or commit:

- `threads.json` cookies/session files
- `sessionid` cookies
- passwords
- API keys
- private account credentials

This repository contains only reusable skill instructions and docs.

---

## Bahasa Indonesia

Skill pack reusable untuk automation Threads dengan agent AI: engagement kontekstual, reply-back, posting visual, verifikasi hasil, dan polish tulisan agar tidak terasa generik/AI banget.

### Skill yang disertakan

- `threads-engagement` — workflow Threads untuk posting, like, reply, reply-back, cek duplikat, penyesuaian bahasa, dan verifikasi.
- `avoid-ai-writing` — skill polish tulisan untuk mengurangi caption/reply yang terlalu generik atau terasa seperti AI. Berdasarkan skill `avoid-ai-writing` MIT-licensed dari Conor Bronsdon.

### Cara install

```bash
npx skills add reydenim/threads-automation-skills --all
```

Atau install manual dengan copy folder skill ke direktori skills agent kamu.

### Bisa bantu agent untuk

- Restore/cek session login Threads dari cookie lokal.
- Membuat post Threads dengan gambar + caption.
- Membalas activity/notification secara kontekstual.
- Menyesuaikan bahasa reply dengan bahasa post target.
- Menghindari reply generic yang terasa seperti AI.
- Cek duplikat sebelum reply.
- Verifikasi reply lewat tab Replies di profile.
- Membuat report rapi dengan status `VERIFIED`, `FAILED`, atau `UNVERIFIED`.

### Setup yang dibutuhkan

1. Ganti `@YOUR_USERNAME` di prompt/cronjob dengan username Threads kamu.
2. Export cookie/session Threads milikmu sendiri memakai Cookie-Editor atau extension sejenis.
3. Simpan cookie secara lokal, contoh:

```text
~/.hermes/browser-sessions/threads.json
```

Jangan pernah commit atau membagikan file cookie/session kamu.

### Contoh cron prompt

```text
Setiap 45 menit, jalankan balanced Threads engagement untuk @YOUR_USERNAME memakai browser session yang tersimpan di ~/.hermes/browser-sessions/threads.json.

- Verifikasi login dulu.
- Scan candidate posts atau activity notifications.
- Like maksimal 5 post per run.
- Reply maksimal 1 kali per run.
- Sesuaikan bahasa reply dengan bahasa post target.
- Jalankan avoid-ai-writing pass sebelum posting.
- Cek duplikat sebelum reply.
- Verifikasi reply lewat https://www.threads.com/@YOUR_USERNAME/replies sebelum melaporkan sukses.
- Laporkan status VERIFIED / FAILED / UNVERIFIED secara jujur.
```

### Catatan keamanan

Jangan pernah share atau commit:

- file cookie/session `threads.json`
- cookie `sessionid`
- password
- API key
- credential akun pribadi

Repo ini hanya berisi instruksi skill reusable dan dokumentasi. Tidak ada cookie, session pribadi, API key, atau credential.

## License

MIT. The included `avoid-ai-writing` skill is MIT-licensed and credited to Conor Bronsdon in its frontmatter.
