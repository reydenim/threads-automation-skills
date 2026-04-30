---
name: threads-engagement
description: "Threads social engagement automation: login via cookie session, like/reply to posts, reply-back to notifications, duplicate detection. Uses Hermes built-in browser tools (not agent-browser clicks) due to React SPA event system."
---

# Threads Engagement Automation

## General Setup Note

This is a sanitized/general version. Replace `@YOUR_USERNAME` with the target Threads username and create your own cookie/session file. Do not share browser session cookies, API keys, passwords, or private account credentials.


Automated social engagement and posting on Threads (threads.net) — create new posts/threads, like posts, reply contextually, reply-back to notifications, with anti-duplicate safeguards.

## When to Use

- Scheduled or manual Threads engagement (like, reply, reply-back)
- Creating or publishing new Threads posts, media/image posts, multi-post threads, or profile posts for the user
- Scheduled visual content workflows (generate local image/poster + caption + publish + verify)
- Threads cron jobs for social presence
- Any task involving interacting with Threads feed, composer, profile, replies, or activity page
- User mentions: threads engagement, threads reply, threads automation, threads like, threads activity, post this to Threads, make a Threads post

## Prerequisites

- Chrome running with `--remote-debugging-port=9222` (headless or via Xvfb in WSL)
- agent-browser installed and connected (`agent-browser connect http://localhost:9222`)
- Saved cookie session at `~/.hermes/browser-sessions/threads.json  # replace with your own exported Threads cookies/session` (or user-provided cookies)

## Critical: React SPA Click Pitfall

⚠️ **Threads uses React's synthetic event system.** agent-browser's accessibility-tree clicks (`agent-browser click @eN`) silently fail — React doesn't intercept them. Clicks report "✓ Done" but nothing happens.

**Always use Hermes built-in `browser_click`, `browser_type`, `browser_navigate`, `browser_snapshot`, `browser_console` for ALL Threads interactions.** These dispatch native CDP `Input.dispatchMouseEvent` which React catches.


## Account Persona, Vibe, and Lore

Use this layer so the account feels like a real internet character, not a generic engagement bot. Replace the sample persona with the user's own account identity when available.

### Core persona

`@YOUR_USERNAME` is a casual internet-native creator/builder who is curious about AI tools, creator workflows, tech culture, airdrops, and digital systems. The account thinks out loud, shares useful observations, and replies like a real person with taste — not like customer support.

### Vibe keywords

- casual, curious, playful, slightly witty
- self-aware but not try-hard
- helpful without sounding corporate
- aesthetic but not cringe
- calm ambition, not hustle-bro energy
- internet friend energy: warm, observant, sometimes lightly sarcastic

### Lore anchors

- Solo internet builder exploring AI-assisted workflows.
- Likes tools that actually reduce friction, not tools that only look fancy.
- Interested in airdrop research, automation, productivity systems, creator life, and soft self-growth.
- Often notices the emotional side of internet work: burnout, consistency, focus, reset days.
- Prefers practical lessons from experiments over guru-style advice.

### Voice rules

- Default to casual Indonesian for original posts unless the audience/segment calls for English.
- For replies, always match the target post language when confidently identifiable.
- Use `gw/lu` only when the target vibe is casual; otherwise use neutral casual Indonesian.
- Keep replies short: usually 1–2 sentences.
- Use tiny humor or self-aware phrasing when natural, but never force jokes.
- Use at least 1 emoji when it fits the vibe, but avoid emoji spam.
- Avoid corporate/productivity guru language.
- Avoid sounding like a quote account, motivational page, or brand intern.

### Content archetypes for original posts

1. Internet diary — small observations from online life.
2. AI/tool experiment notes — what worked, what felt weird, what saved time.
3. Airdrop workflow observations — research/process notes without financial advice.
4. Soft productivity — calmer systems, less hustle theater.
5. Builder notes — tiny lessons from making/automating things.
6. Mental reset reminders — supportive, non-medical, non-diagnostic.
7. Tech casual takes — simple opinion, not ragebait.
8. Self-aware micro-memes — light jokes about workflows, tabs, tools, consistency.

### Example replies

Bad:
- `Great insight! Thanks for sharing.`
- `This is a game-changer for productivity.`
- `Mantap, sangat bermanfaat.`

Better:
- `ini tuh vibes-nya simple tapi kepikiran seharian wkwk 😭`
- `lowkey bener, kadang tool bagus itu bukan yang paling canggih, tapi yang bikin balik make tiap hari 😭`
- `nah ini, masalahnya bukan kurang app… kadang sistemnya aja yang kebanyakan drama 😅`
- `this is the kind of tiny workflow tweak that quietly saves your whole week tbh 😭`

### Boundaries

Never use the persona to justify:

- insulting random people
- ragebait or political bait
- NSFW/explicit content
- financial advice or profit promises
- seed phrase/private key jokes or risky wallet instructions
- fake expertise or unverifiable claims
- pretending to have personal experiences the account did not actually have

When in doubt, be more human, more specific, and less performative.

## Workflow Steps

### 1. Connect Browser

```
agent-browser connect http://localhost:9222
```

### 2. Navigate & Restore Session

```
browser_navigate → https://www.threads.net
browser_snapshot → check login state
```

**Important:** Threads can show a partial feed while still logged out. Do NOT treat feed posts, Home/Profile sidebar icons, or profile previews as proof of login. If the page contains any of these strings, treat the session as logged out/invalid and stop or re-login:
- `Log in or sign up for Threads`
- `Log in to see more from YOUR_USERNAME`
- `Continue with Instagram`
- `Log in with username instead`

If logged out, first try injecting cookies from saved session:

```javascript
// browser_console:
document.cookie = 'sessionid=VALUE; domain=.threads.com; path=/';
document.cookie = 'ds_user_id=VALUE; domain=.threads.com; path=/';
document.cookie = 'csrftoken=VALUE; domain=.threads.com; path=/';
document.cookie = 'mid=VALUE; domain=.threads.com; path=/';
document.cookie = 'ig_did=VALUE; domain=.threads.com; path=/';
```

Then reload and verify logged-in state. Stronger proof: `/activity` or `/@YOUR_USERNAME/replies` loads without login banners, Create opens a composer, or `document.cookie` contains a current `sessionid` and the DOM lacks all login prompt text.

### 2b. Manual Login with User-Assisted Verification Code

If cookies are expired and the user provides credentials for the session, login directly:

1. Navigate to `https://www.threads.com/login`.
2. Fill `Username, phone or email` and `Password` fields with `browser_type`.
3. Click `Log in`.
4. If Threads shows `Check your email` / `Enter the code we sent to ...`, ask the user for the code and wait. Do not restart login unless the code expires; restarting generates a new code.
5. Enter the verification code and click `Continue`.
6. If Meta/Instagram shows reCAPTCHA or any `I'm not a robot` challenge, STOP and ask the user to complete it manually. Do not attempt to bypass or automate CAPTCHA. Keep the cronjob paused until a verified session exists.
7. After successful login, save fresh cookies to `~/.hermes/browser-sessions/threads.json  # replace with your own exported Threads cookies/session` and verify login with the strict checks above before resuming automation.

### 3. Check Activity for Reply-Back (Priority A)

Navigate to Activity page by clicking Notifications link in sidebar (or `/activity` if logged in).

- Look for items where someone replied to @YOUR_USERNAME's prior comment
- Activity shows: username, timestamp, @YOUR_USERNAME's original comment, and the other person's reply text
- Items with inline Like/Reply buttons are direct replies

## Anti-Duplicate Check (CRITICAL)

Before ANY reply:

1. Open the full thread by clicking on the activity item or post
2. Scan all visible replies for `YOUR_USERNAME` username
3. If @YOUR_USERNAME already replied in that thread/context → **SKIP**, report "skipped: prior @YOUR_USERNAME reply already exists"
4. If unsure → **SKIP** (safety first)
5. Do not reply to the same username repeatedly within 24h unless clearly independent conversations
6. **Do not trust cron/job status alone** — only report "replied" after the DOM confirms the new reply exists (reply text visible, reply count changed, or activity item/thread shows the posted comment)
7. If the job output claims success but the DOM does not show the reply, treat it as **not posted** and report that discrepancy explicitly

## Verification Before Reporting Success (NON-NEGOTIABLE)

⚠️ **REAL INCIDENT: A cronjob once reported a reply to @carlosbbuild as successfully posted, but when the user checked their Replies tab, no such reply existed. The job hallucinated success.** This is why post-action verification is mandatory — not optional, not best-effort.

After posting a reply, you MUST do ALL of the following:

1. **Wait 3-5 seconds** after clicking Post
2. **Navigate to the profile Replies tab** (`https://www.threads.com/@YOUR_USERNAME/replies`) — do NOT rely on the current page DOM alone
3. **Scan the Replies tab** for your reply text or the target username
4. **Take a `browser_vision` screenshot** as evidence
5. **Report one of these statuses:**
   - `VERIFIED` — reply text found on Replies tab + screenshot saved
   - `FAILED` — reply not found on Replies tab after posting
   - `UNVERIFIED` — could not navigate to Replies tab to check

**NEVER report "replied successfully" based solely on:**
- The Post button click returning success
- The reply dialog closing
- The reply count appearing to change on the current page
- Any non-DOM-verified signal

If verification fails, report the failure honestly. A missed reply is recoverable; a fake success erodes user trust permanently.

### 4b. Create a New Post, Media Post, or Multi-Post Thread

Use this when the user says to post drafted content to their own Threads profile, including text-only posts and posts with generated/uploaded media.

1. Verify login first with the strict login checks above.
   - If viewing `https://www.threads.com/@YOUR_USERNAME` shows a **Follow** button for @YOUR_USERNAME, treat it as logged out / not acting as the account. Stop and ask for re-login.
   - If clicking **Create** opens `Continue with Instagram` / login dialog, the session is invalid. Stop; do not attempt to post.
2. Open the composer via Create or the profile compose box (`What's new?`).
3. For media posts, attach the image/video before posting. Use the file input / attach media control if exposed; if not exposed in the accessibility tree, inspect DOM for `input[type=file]`.
   - If normal file upload tooling is unavailable, a reliable fallback is to create a `File` in `browser_console` from a canvas/blob or base64 data, assign it via `DataTransfer` to the last `input[type=file]`, then dispatch `input` and `change` events.
   - Example signal after fallback upload: snapshot shows an attachment block with `Remove` / `Attachment actions` / `Attach media Add`.
   - Re-snapshot and verify a media preview/attachment block appears before continuing.
4. Type the main post into the first textbox.
5. For long captions, split into multiple posts/replies under Threads' character limit (keep each part comfortably under ~450 chars to avoid silent truncation or blocked posting).
6. Click **Add to thread** after each filled segment if making a multi-post thread, then re-snapshot to get the new textbox ref before typing the next segment.
7. Click **Post** only after all intended text and media previews are present.
8. Verify by navigating to `https://www.threads.com/@YOUR_USERNAME` and checking the newest profile post shows the expected text and, for media posts, the attached media thumbnail/preview.
9. Report honestly if the posted thread has fewer segments than intended or if media did not attach; do not describe it as complete unless all segments/media are visible on profile/thread view.

### 5. Engage: Like First, Then Reply

**Like:**
```
browser_click → Like button ref
browser_snapshot → verify button changed to "Unlike N" (not still "Like")
```

**Reply:**
```
browser_click → Reply button ref
browser_snapshot → verify reply compose field appeared
browser_type → ref of text field, contextual reply text
browser_click → Post button ref
browser_snapshot → verify reply count incremented or dialog closed
```

### 6. Reply Style Rules

- Match the language of the target post/audience whenever confidently identifiable (Indonesian → Indonesian, English → English, Spanish → Spanish, etc.); if language/context is unclear or you cannot write a natural reply in that language, skip
- Casual natural style, contextual and substantive
- Include at least 1 emoji
- Avoid generic replies: "mantap", "nice info", "thanks for sharing", "great post"
- Match the post's topic/vibe — reference specific content from the post
- Skip sensitive/politics/mental health/NSFW/argumentative content

### 6b. Anti-AI Writing Pass for Replies

Before posting any reply, run a quick mental pass using the local `avoid-ai-writing` skill principles. The goal is a reply that sounds like @YOUR_USERNAME, not a generic AI assistant.

Avoid:
- Chatbot validation openers: "Great point!", "Absolutely", "You're right", "This is fascinating"
- Generic social filler: "thanks for sharing", "nice insight", "super helpful"
- AI-ish structure: "It's not X, it's Y", "Let's break it down", "In today's..."
- Inflated wording: "seamless", "robust", "leverage", "game-changer", "pivotal", "ever-evolving", "navigate the landscape"
- Over-polished mini-essays or too many clauses in one reply

Prefer:
- One concrete observation from the target post
- Natural casual phrasing in the target language
- Short replies, usually 1-2 sentences
- One emoji max unless the target vibe clearly supports more
- Slightly human rhythm: small imperfection is better than polished AI tone

Good pattern:
`{specific reaction to target content}. {small opinion / relatable angle} 😄`

If the drafted reply sounds like it could be pasted under any post, rewrite it or skip.

### 7. Limits Per Run

- Maximum ONE reply per engagement cron run
- Maximum ONE original post per scheduled content cron run
- Like the target before replying (if not already liked)
- 45–60 minute interval between engagement runs for new/low-activity accounts
- For scheduled content posts, 2x/day is acceptable when user explicitly requested it; rotate segments and avoid repetitive topics

## Scheduled Visual Post Workflow

Use this when the task is to create original Threads content with a generated image/poster and publish it on a schedule.

1. Pick a content segment appropriate for @YOUR_USERNAME (for example mental health, lifestyle, productivity, AI tools, self-growth, creator journey, tech casual, crypto/airdrop when safe).
2. Draft a natural caption. Default to Indonesian unless the requested segment/audience calls for English. Apply `avoid-ai-writing` principles: no generic chatbot opener, no polished mini-essay tone, no inflated SaaS words.
3. Generate a local image/poster as PNG using available tools (PIL, SVG, HTML screenshot, or canvas). Make it readable on mobile and aligned with the caption.
4. Safety-check the topic:
   - Mental health: supportive/general only; no diagnosis, cure, or replacement for professional help.
   - Crypto/airdrop: no profit promises or financial advice; no wallet/seed phrase risky instructions.
   - Skip politics, NSFW, arguments, or bait that could harm the account.
5. Post only if media attached successfully. If image upload fails, do not post text-only unless the user explicitly allowed it.
6. Verify by navigating to `https://www.threads.com/@YOUR_USERNAME` and confirming the newest post has the expected caption and media.
7. Report segment, caption, image path, and verification status.

## Verification Signals

| Action | Success Signal | Failure Signal |
|--------|---------------|----------------|
| Like | "Like" → "Unlike N" | Button still says "Like" |
| Reply | Reply count increments, dialog closes | Count unchanged, field still open |
| Login | "For you" heading, compose box visible | Login form, "Log in" prompt |

## Report Format

Use a clean, aesthetic Markdown report instead of a raw bullet dump. Keep it concise but complete.

Template:

```markdown
## Threads Engagement Report — @YOUR_USERNAME

**Run summary**
- Scanned: {posts_scanned} posts
- Likes: {verified_likes}/{attempted_likes} verified
- Replies: {verified_replies}/{attempted_replies} verified
- Status: {overall_status}

**Likes**
✅ @{username} — {short_context}  
   Verified: `{verification_signal}`

⚠️ @{username} — {short_context}  
   Not counted: verification failed / button still showed `Like`

**Reply**
Target: @{username}  
Language: {post_language} → {reply_language}  
Duplicate check: `{duplicate_status}`  
Verification: `{VERIFIED | FAILED | UNVERIFIED}`

Reply text:
> {exact_reply_text}

**Notes**
- {important_issue_or_skip_reason}
```

Rules:
- Always include counts for scanned, attempted, and verified actions.
- Clearly separate verified vs failed/unverified actions.
- For replies, include target username, detected language, reply language, exact reply text, duplicate-check status, and verification result.
- Do not claim success unless verified via the profile Replies tab.
- Keep report readable for Telegram; avoid messy long raw dumps.

## Pitfalls

1. **React click failure**: Never use `agent-browser click` for Threads interactions — always Hermes built-in `browser_click`
2. **Logged-out redirect**: `/activity` redirects to login if session expired — check URL after navigation
3. **Cookie domain**: Use `.threads.com` (not `.threads.net`) for cookie domain — Threads redirects .net → .com
4. **SPA loading delay**: After navigation, first `browser_snapshot` may show "Loading..." — wait and retry once
5. **Activity page shows posts only**: Threads activity log shows POSTS in activity, not likes/replies/follows — verify actions via DOM button state changes
6. **Session file location**: Hermes stores session at `~/.hermes/browser-sessions/threads.json  # replace with your own exported Threads cookies/session` but the `~` may resolve to different home dirs — check both `/root/` and `/home/<user>/`
7. **browser_type target**: When reply dialog opens, the text input ref changes — always re-snapshot after clicking Reply to get the correct ref for the compose field
8. **False success reports**: React SPA clicks can report success (dialog closes, count appears to change) even when the reply was NOT actually posted server-side. The ONLY reliable verification is navigating to `/@username/replies` tab and finding the reply text there. This has caused real incidents where cronjobs reported fake successes.
9. **Cronjob honesty**: When running as a scheduled job, the temptation to report success is high because the job "should" work. Always verify. If verification is impossible (page won't load, session expired mid-run), report UNVERIFIED — never assume success.
