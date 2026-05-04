# Corbett Road · Pitch Studio

A fully-free, locally-run training system for advisors. Voice-based AI-prospect simulations with consultative-pitch report cards, all in one HTML file.

## What this is

A single-page web application that lets your advisors practice the seven-step consultative pitch with an AI prospect. After each session, an AI coach generates a 1–10 score across all seven categories with evidence-based feedback. Two modes:

- **Pitch mode** — Live, unassisted peer pitch.
- **Practice mode** — Same prospect, with a coach panel suggesting "best answer" responses in real time.

## Total cost: $0/month

| Component | Provider | Cost |
|---|---|---|
| Hosting | Runs locally on advisor's PC | $0 |
| Voice (speech → text) | Chrome Web Speech API | $0 |
| Voice (text → speech) | Chrome Speech Synthesis | $0 |
| AI prospect + coach + grader | Groq free tier | $0 |
| Storage | Browser localStorage | $0 |
| Auth | Email-domain gate (corbettroad.com) | $0 |

## Requirements

- **Chrome browser** (or Edge — both Chromium-based work). Firefox and Safari will not work properly.
- **Windows 10 or 11** PC with a working microphone.
- **Python 3** *(comes with Windows 10/11 — see below if not)*. Used only to serve the HTML file locally so Chrome will grant microphone permission.
- **One-time:** A free Groq API key.

---

## First-time setup (5 minutes)

### For each advisor on your team

When an advisor signs in for the first time, the app walks them through a 3-step setup wizard:
1. **Welcome** — a quick overview of what they'll need.
2. **Connect Groq account** — instructions to grab a free key at console.groq.com/keys (no credit card), paste it in, and test the connection.
3. **Pick a voice** — choose one of the browser's built-in voices for the AI prospect (or let it auto-select).

The whole thing takes about 3 minutes. They'll only see it once — after setup, they go straight to the studio every time.

### Why each advisor uses their own key

Groq's free tier gives each account its own rate limits (30 requests/minute, 14,400/day). With 5–7 advisors each using their own free key, your team has 5–7× the capacity, all free, with no rate-limit conflicts. **Do not share a single key across the team.**

### Verify Python is installed (one-time, per machine)

The app needs Python to run a tiny local web server (so Chrome will grant microphone access). Open Command Prompt (`Windows + R`, type `cmd`, Enter) and run:

```
python --version
```

If you see `Python 3.x.x`, you're set. If you see "Python was not found", install it from <https://www.python.org/downloads/> — make sure to check **"Add Python to PATH"** during install.

### Launching the app

1. Save the `index.html`, `start.bat`, and `README.md` files to a folder on each advisor's PC, e.g. `C:\PitchStudio\`.
2. Double-click **`start.bat`** — a black command window will open and Chrome will launch the app at `http://localhost:8000`.
3. *(Or manually: open Command Prompt in the folder, run `python -m http.server 8000`, then open Chrome to `http://localhost:8000`.)*
4. Sign in with the advisor's `@corbettroad.com` email.
5. Complete the 3-step setup wizard (Groq key + voice).
6. You're ready to pitch.

**Important:** The app must be opened from `http://localhost:8000`, not by double-clicking the HTML file directly. Chrome will not grant microphone permission to local files.

---

## Day-to-day use

1. Run `start.bat` (or `python -m http.server 8000` in the folder).
2. Open Chrome to `http://localhost:8000`.
3. Sign in.
4. Pick **Pitch mode** or **Practice mode**.
5. Click **Set Up Prospect** → fill in the client details (or click **Generate Prospect** for a random scenario) → **Begin Session**.
6. Click the microphone and start the meeting. Speak naturally; pause briefly between thoughts. The prospect will respond.
7. When you're done, click **End & Get Report Card**.
8. Review your scores, save/print as PDF if you want, or revisit anytime under **Report Cards**.

### Tips for best voice quality

- Use a quiet room. Background noise hurts speech recognition accuracy.
- Speak naturally — don't shout or over-enunciate.
- Pause 1–2 seconds between thoughts. The system uses silence to know when you've finished a turn.
- On Windows, the best built-in voices are "Microsoft Aria Online", "Microsoft Jenny Online", "Microsoft Guy Online", and "Microsoft Tony Online". Pick one in Settings → Prospect Voice.

---

## Privacy & data handling

- **Voice never leaves your computer.** All speech recognition and audio playback happens locally in Chrome.
- **Conversation transcripts** are sent to Groq for the AI prospect's responses and report-card grading. Groq's free tier does not train on your data, but treat this as you would any cloud AI service.
- **Report cards** are stored only in your browser's local storage (this device only).
- **Never enter real client information.** Use synthetic personas only.
- **API key** is stored in your browser's local storage. Anyone with access to your machine could read it. Treat it like a password.

---

## Troubleshooting

**The microphone button is dim / nothing happens when I click it.**
You probably opened the file directly (`file://...`) instead of through the local server. Close the tab, run `start.bat`, and visit `http://localhost:8000`.

**Chrome asks for microphone permission and I clicked "Block".**
Click the lock icon (🔒) in the address bar → Site Settings → set Microphone to "Allow", then reload.

**"Couldn't reach the AI prospect" error.**
Your Groq API key is invalid, expired, or you're rate-limited. Go to Settings → click Test Connection. If it fails, regenerate the key at <https://console.groq.com/keys>.

**Voice sounds robotic.**
Default Windows voices are okay, not great. In Settings, try selecting one of the "Online" voices (Microsoft Aria Online, Jenny Online, etc.) — they're noticeably more natural. Some require Windows 11.

**Speech recognition is missing words.**
Web Speech API isn't perfect on financial jargon. Speak slightly slower around terms like "Roth", "RMD", "401(k)", and "fiduciary". A USB headset mic helps a lot if accuracy is consistently poor.

**The prospect breaks character or sounds robotic.**
The free Groq tier uses Llama 3.1 8B for the prospect — it's fast but occasionally goes off-script. If this becomes a regular complaint, the natural upgrade is to swap in Claude Haiku ($2/month for your team) for noticeably more believable prospects. Edit the `MODEL_PROSPECT` constant in `index.html`.

**Groq says "rate limit exceeded".**
Free tier allows ~30 requests/minute, plenty for normal use. If you hit it, wait 60 seconds. If it's a constant problem, you have heavy usage and may want to upgrade to Groq's paid tier (still very cheap, ~$0.05 per session).

**I want to back up my report cards.**
Open Chrome DevTools (F12) → Application tab → Local Storage → `http://localhost:8000` → copy the value of `crpt_reports`. Save that JSON somewhere. To restore, paste it back.

---

## What's in the file

`index.html` is a self-contained React application. All code, styling, and logic in one file. Loads React, Tailwind, and Babel from CDNs. No build step. No dependencies to install.

Constants you might want to edit (near the top of the `<script>` tag):

- `COMPANY_DOMAIN` — change to a different email domain if needed.
- `MODEL_PROSPECT` / `MODEL_COACH` / `MODEL_GRADER` — swap in different Groq models.
- The system prompts (`buildProspectSystemPrompt`, `buildCoachSystemPrompt`, `buildGraderSystemPrompt`) — tune the prospect personality, coaching style, or rubric language.

---

## Phase roadmap (what's built vs. what's next)

This is the v1 / Phase 1+2+3 build. Here's what's included and what comes next.

**Built (v1):**
- Email-domain login (gate, not full auth)
- Pitch mode + Practice mode toggle
- Voice-based AI prospect with Web Speech API
- Persona setup (manual or AI-generated)
- 7-category report card generation with evidence
- Saved report card history
- Settings panel for API key + voice selection

**Phase 2 (next, if/when needed):**
- Real authentication via Microsoft 365 SSO
- Server-side storage so report cards sync across devices
- Manager dashboard to view all advisors' history
- Custom rubric weights per category
- Persona library (saved scenarios you can replay)

**Phase 3 (future):**
- Audio recording playback alongside transcript
- Side-by-side "what you said" vs. "what an expert would have said" replay
- Aggregate trend analytics (improvement over time, common gaps)
- Export to PDF with company branding

---

## Support

This is a self-hosted internal tool. If something breaks, check the Troubleshooting section first, then open Chrome DevTools (F12 → Console) and look for errors — they're usually self-explanatory.
