# Threads Automation Skills

Reusable agent skills for Threads automation: contextual engagement, reply-back, visual post publishing, verification, and anti-AI writing polish.

## Skills included

- `threads-engagement` — Threads workflow for posting, liking, replying, reply-back, duplicate checks, and verification.
- `avoid-ai-writing` — writing polish skill to reduce generic/AI-ish captions and replies. Based on Conor Bronsdon's MIT-licensed `avoid-ai-writing` skill.

## Install

```bash
npx skills add reydenim/threads-automation-skills --all
```

Or install manually by copying the skill folders into your agent skills directory.

## What it helps agents do

- Restore/check Threads login session from local cookies
- Create Threads posts with image + caption
- Reply contextually to activity/notifications
- Match the target post language
- Avoid generic AI-sounding replies
- Check duplicates before replying
- Verify posted replies via the profile Replies tab
- Produce clean reports with VERIFIED / FAILED / UNVERIFIED status

## Required setup

1. Replace `@YOUR_USERNAME` in your prompts/cronjobs with your Threads username.
2. Export your own Threads cookies/session using Cookie-Editor or a similar extension.
3. Save your cookies locally, for example:

```text
~/.hermes/browser-sessions/threads.json
```

Do **not** commit or share your cookie/session file.

## Example cron prompt

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

## Security notes

Never share or commit:

- `threads.json` cookies/session files
- `sessionid` cookies
- passwords
- API keys
- private account credentials

This repository contains only reusable skill instructions and docs.

## License

MIT. The included `avoid-ai-writing` skill is MIT-licensed and credited to Conor Bronsdon in its frontmatter.
