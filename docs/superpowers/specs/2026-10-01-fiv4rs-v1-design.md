# FIV4RS v1 design spec

**Your journey to 5.4.** Powered by Enforcer Fitness.

| | |
|---|---|
| Status | Draft for Chris's review |
| Date | 1 October 2026 |
| Owner | Chris (product) · Jon Shorland, Enforcer Fitness (training content) |
| Repo | `Lawrosnaps/FIV4RS` |
| Companion doc | [`docs/jon/questions-for-jon.md`](../../jon/questions-for-jon.md): training decisions Jon must confirm |

---

## 1. What we're building and why

FIV4RS is a free mobile app for UK police officers who feel unfit, anxious or unsure about the Job Related Fitness Test (JRFT). It builds the fitness and confidence to reach **level 5.4** on the 15m multistage shuttle test.

The aim is that an officer arrives at their JRFT prepared, already familiar with the test, and confident in their progress.

FIV4RS is also the **officer side of a future police-fitness coaching platform**, "Everfit, niched down". Jon Shorland (Enforcer Fitness) is the first coach. v1 ships the officer app only, but every part of it is shaped so that coaching plugs in without officers changing app, account or history (see §6).

### Success looks like
- An officer can go from install to their first guided session in under 3 minutes.
- Officers practise with real JRFT timing, so test day holds no surprises.
- Officers who reach 5.4 can move into coaching with Jon in one tap, carrying their history with them.
- Every coaching request is logged, so referrals to Enforcer Fitness can be counted.

### Tone principles
1. **Encouraging, never judging.** There are no streaks to break, no red "missed" marks and no shame copy. Progress is framed as effort plus improvement.
2. **Shift-proof.** Sessions belong to a flexible week, not fixed days. A short session after nights still counts.
3. **Calm and premium.** Big, clear numbers, few choices per screen, and a confident dark look.
4. **Private by default.** Nothing leaves the officer's account unless they tap to share it.

---

## 2. Decisions already made

| Topic | Decision |
|---|---|
| Platform | Expo (React Native) app for iOS, Android and web, in its own repo |
| Brand | FIV4RS stands alone, with no SHFTRS branding. "Powered by Enforcer Fitness" lockup |
| Logo | Chris's v2 design (oversized volt 4 with arrow). Files in `/mnt/project-files/fiv4rs/brand/fiv4rs/` |
| Look | "Night track": charcoal `#151719`, off-white `#F5F4EC`, volt `#D4FF3F` |
| Plan setting | Built back from the officer's test date. "Not sure yet" gives an ongoing programme that switches to a dated plan when a date is added |
| Shifts | Flexible week: N sessions per week, any day, plus a gentle short option. No rota input and no SHFTRS link |
| Data | Accounts from day one (Supabase), mainly so FIV4RS can bring clients to Enforcer Fitness |
| Leads | Opt-in "Get coached by Jon". The officer chooses exactly what is shared |
| Money | Free to officers. Income from a referral share with Jon, then coach subscriptions on the coaching platform. Force or Federation licensing is later upside |
| Training content | Everything training-related is proposed here as a default and **must be confirmed by Jon** before launch |

---

## 3. Scope

### In v1
- Email sign-up and sign-in, password reset, account deletion and data export
- Onboarding: test date or "not sure yet", current activity level, sessions per week, optional baseline run
- **Today**: the next suggested session, a short-session option and the test countdown
- **Plan**: this week plus the weeks to test day, rebuilt when inputs change
- **Shuttle**: an audio-cued JRFT runner (15m and 10m modes) and a listen-and-learn mode
- **Progress**: best level over time, sessions completed and a readiness meter
- **Coach**: the "Get coached by Jon" request with a sharing choice, plus the 5.4-to-Jon transition moments (§6)
- Health and safety screening before the first run
- Works offline: everything is logged on the phone and synced later

### Not in v1
- Coach dashboard or coach app (the next project; the data model is ready for it)
- Payments or subscriptions
- Rota import or any SHFTRS link
- Social features, leaderboards and streaks
- Wearable or GPS integrations
- Push notifications beyond one optional weekly nudge (see §9.6)

---

## 4. JRFT reference facts

Source: College of Policing, *Implementation guidance for fitness testing* (August 2021).

- The standard is **5.4**: running at least in time with the bleeps for four shuttles at level 5.
- The test takes **3 min 35 s** and covers **525 m** (35 shuttles of 15 m).

| Level | Shuttles | Seconds per shuttle | Level time | Cumulative |
|---|---|---|---|---|
| 1 | 7 | 6.9 | 48.3 s | 0:48.3 |
| 2 | 8 | 6.4 | 51.2 s | 1:39.5 |
| 3 | 8 | 6.1 | 48.8 s | 2:28.3 |
| 4 | 8 | 5.6 | 44.8 s | 3:13.1 |
| 5 (to 5.4) | 4 | 5.5 | 22.0 s | **3:35.1** |

The arithmetic matches the published 3 min 35 s and 525 m. The engine must reproduce these figures exactly (§8.3).

**Still to verify before launch** (with Jon or the College): how warnings work when an officer misses a bleep, and whether any official cue audio or wording should be mirrored. FIV4RS uses its **own** beep sounds and voice; it never reproduces official recordings.

Specialist roles use higher standards on the same scale. policepay.co.uk, citing the College of Policing, gives 6.3 for public order (PSU), 5.7 for dog handlers, 7.6 for AFO, 9.4 for ARV and 10.5 for DIAFO. These are a natural "next goal" with Jon (§6). The figures need verifying before they appear in the app. Supporting levels above 5 needs the timing table for levels 6 and up, and that is not in v1.

---

## 5. The officer's journey

### 5.1 Welcome and account
1. **Welcome:** the FIV4RS logo, "Your journey to 5.4", "Powered by Enforcer Fitness", **Get started**, **I have an account**.
2. **Create account:** email and password. Name is optional at this point and only required if they later request coaching.
3. **Safety check** (§10.1): a short readiness questionnaire. Any "yes" answer shows advice to speak to a GP or Occupational Health first. The officer can still continue, which is their choice and is recorded.

### 5.2 Onboarding (about 1 minute, one question per screen)
1. "When's your fitness test?": a date picker, or **Not sure yet**.
2. "How active are you right now?": *Not much at the moment*, *Some activity*, *Regularly active*. This is a starting point only.
3. "How many sessions can you fit in most weeks?": 2, 3 or 4 (default 3). The copy says shifts are expected and the plan flexes.
4. "Want to find your starting level?": **Try a practice run** (the shuttle in baseline mode, stopping whenever they choose) or **Skip, start gently**.
5. Plan ready: a summary such as "11 weeks to test day · 3 sessions a week · Starting from level 3.2".

### 5.3 Main tabs
| Tab | What it shows |
|---|---|
| **Today** | The next suggested session card (title, duration, why it matters) with **Start** and **Short version**. Shows the countdown "23 days to test day" or "No date yet". Shows this week's progress as N of 3 sessions done, framed as progress, not debt |
| **Plan** | This week's sessions (any order, any day), then later weeks collapsed by phase (Build, Sharpen, Test week). Changing the test date or sessions per week rebuilds the plan with a gentle confirmation |
| **Shuttle** | Pick **Practice run**, **Intervals** (driven by the session) or **Listen and learn**. Choose 15m or 10m. Lane setup guide, then the runner (§8) |
| **Progress** | Best level over time (line chart), recent runs, sessions completed and the readiness meter (§9.5) |
| **Coach** | The Enforcer Fitness intro, "Get coached by Jon", status of any request, and FAQs about coaching |

Settings (from the profile icon) cover the test date, sessions per week, units and voice cues, data export, account deletion, privacy notice and health disclaimer.

### 5.4 Sessions
A session is a short sequence of **blocks** taken from the content pack (§7): warm-up, shuttle intervals, a continuous shuttle run, strength or mobility, and cool-down. Each block has a duration or a rep target and a short plain-English coaching note from Jon.

- **Short version:** each session has a shorter variant (15 to 20 minutes) for after nights or long shifts. It counts as a completed session.
- **Logging:** completing or ending a session records the actual blocks done, an optional effort rating (1 to 5 emoji scale) and optional notes. Ending early is logged as "done some", never as failed.

---

## 6. The step from 5.4 to Jon's coaching (headline requirement)

Chris's requirement is that moving from "I can do 5.4" to "I'm coached by Jon" must feel like one continuous journey: no new app, no new account, no re-explaining.

### 6.1 The moments that invite it
| Moment | What the officer sees |
|---|---|
| First practice run that reaches 5.4 | A full-screen celebration ("That's test standard. You did it."), then **Keep it locked in** (a maintenance plan to test day) and a soft card: "Want to go further with Jon?" |
| Test day (the app asks the day after the date) | "How did it go?": **Passed**, **Not this time** or **Didn't take it** |
| Passed | Celebration, then a **What's next?** screen. Options: *Stay test-ready* (free maintenance programme), *Train for a specialist role* (PSU, dog handler, firearms and so on: coached by Jon), *General fitness with Jon* |
| Not this time | Supportive copy and a rebuilt plan for the re-test date. "Want a coach in your corner?" is offered once, gently |
| Any time | The Coach tab is always there. It is never a pop-up that blocks the main flow |

### 6.2 One-tap request, pre-filled
Tapping any "with Jon" option opens a single **Share with Jon** sheet. It is pre-filled and every item is a toggle:
- Name and contact: email (required), phone (optional)
- Goal: test-ready, specialist role (which one), general fitness
- Test date and result, if logged
- Current best level and a 4-week trend
- Sessions completed and the usual sessions per week
- A free-text note ("anything Jon should know?")

**Send to Jon** creates a `coaching_request` (§11) and emails Jon a summary. The officer sees "Request sent. Jon will be in touch." with the date, plus a status row on the Coach tab (*Sent*, *Jon has replied*, *Coaching active*). Jon updates the status by replying through a simple link in the email in v1, and through the coach app later.

### 6.3 Why it stays seamless when the coach app arrives
- **Same account and same history.** When Jon accepts, a `coach_link` connects the officer to Jon. The link has explicit consent, can be revoked any time, and has a defined scope of what Jon can see.
- **Same app.** A coach-assigned programme is just another content pack (§7) with `source = coach`. The Today and Plan tabs already render any content pack, so Jon's programme replaces the built-in one in place.
- **No re-onboarding.** The test date, level history and sessions are already in the officer's account, so Jon starts with full context.
- **Reversible.** Ending coaching returns the officer to the built-in programme with their history intact.

In v1, only `coaching_requests` and the consent text ship. The `coaches` and `coach_links` tables are created now, empty and behind feature flags, so the coach app is additive.

---

## 7. Training content pack

All training is **data, not code**, so Jon can change it without an app release.

```text
ContentPack
  id, version, source: "fiv4rs" | "coach", authorId, publishedAt
  phases[]        e.g. Build, Sharpen, Test week, Maintain
  sessions[]      id, title, purpose, phase, durationMin, shortVariantId?, blocks[]
  blocks[]        type: warmup | shuttle_intervals | shuttle_continuous | strength | mobility | cooldown
                  params (per type: target level, reps, rest, distance mode, duration), coachNote
  progression     rules the plan builder uses (§9)
  tips[]          short coaching notes shown on Today, Progress and the shuttle setup screens
```

- The pack is a versioned JSON document validated against a schema (zod) both at build time and when loading.
- A default pack ships inside the app, so the first launch and offline use always work. Newer published packs download from Supabase and are cached.
- Every content field that Jon owns is marked **[Jon to confirm]** in the default pack until he signs it off. The launch checklist fails while any remain.

---

## 8. Shuttle engine

### 8.1 Modes
| Mode | Purpose |
|---|---|
| **Practice run** | The full JRFT sequence from level 1, stopping when the officer taps **Stop** or reaches a chosen target such as 5.4. Records the level reached |
| **Intervals** | Session-driven: for example "3 × level 4 for 4 shuttles, 60 s rest". Parameters come from the content pack |
| **Listen and learn** | No running. Plays the full sequence while showing the current level, shuttle and time, with tips about turning. Builds familiarity and calms nerves |
| **Baseline** | A practice run used in onboarding to set the starting level |

### 8.2 Distances
- **15m** is the true test.
- **10m (short space)** keeps the **same running speed** per level, so each shuttle time is two thirds of the 15m time. Whether 10m is suitable at all, and how much the extra turns change the effort, is **[Jon to confirm]**. The app labels 10m runs clearly and the readiness meter weights them (§9.5).
- The lane setup screen shows how to measure the lane (for example 15 m is about 20 average strides), and suggests cones or two markers and a flat, non-slip surface.

### 8.3 Timing
- `packages/core/shuttle` is a pure TypeScript module. Given (distance, mode, start level, target), it produces a schedule of cues `{atMs, type: beep | level_up | countdown | finish, level, shuttle}`.
- Unit tests assert the exact §4 table: 35 shuttles, a cumulative 215.1 s at 5.4, and the per-level shuttle counts and times. The 10m schedule is the same cues with times scaled by 10/15.
- **Playback** is scheduled against a monotonic clock, not chained timers, so drift cannot accumulate. Target accuracy is within ±20 ms of the schedule over 3:35, verified on real devices (§13).
- **Audio:** bundled beep and voice samples ("Level 3", "Last shuttle", "Five four!"). Plays with the screen locked and through headphones or Bluetooth (iOS background audio mode; Android foreground service with a notification). The screen is kept awake while the runner is open.
- **Visuals:** a huge level.shuttle readout (for example **4.6**), a progress ring for the current shuttle, the next-beep countdown and a one-tap **Stop**.
- **Haptics:** a light tap on each beep, which is optional.

---

## 9. Plan builder

A pure TypeScript module, `packages/core/plan`. The inputs are the test date (optional), today's date, the starting level, sessions per week and the content pack's progression rules. The output is a week-by-week list of session IDs. It is deterministic, so the same inputs always give the same plan.

### 9.1 Dated plan (proposed defaults, **[Jon to confirm]**)
- Count back whole weeks from the test date. The final week is **Test week**: lighter work, one confidence run to 5.4 three or four days before, and rest the day before.
- The remaining weeks split roughly two thirds **Build** and one third **Sharpen**.
- Target shuttle levels rise in small steps from the starting level towards 5.4 and beyond (for example 5.6 to 5.8 in Sharpen), so the test feels comfortable.

### 9.2 Short runway
If there are fewer than 3 weeks to the test, the app shows a compressed plan with an honest, kind note ("Short runway: we'll focus on familiarity and pacing"). It never shows scary copy.

### 9.3 Long runway or no date
- More than 16 weeks out, the plan starts with a **Foundation** phase.
- **Not sure yet** gives a rolling Build programme. It progresses week to week from the latest best level and becomes a dated plan as soon as a date is entered, keeping the history.

### 9.4 Adapting
- Sessions not done in a week simply roll off. The next week is based on what was actually done and the latest level, never "catch up".
- If the best level hasn't improved in about 3 weeks, the next week repeats the current step with a reassuring tip. The exact rule is **[Jon to confirm]**.

### 9.5 Readiness meter
A 0 to 100 "test-ready" meter on Progress and Today. The proposed formula is **[Jon to confirm]**:
- 60% from the best 15m practice level, measured against 5.4. A 10m run counts at a discount until a 15m run confirms it.
- 25% from consistency: sessions done in the last 4 weeks against the plan.
- 15% from familiarity: at least one full listen-and-learn and at least one full 15m practice run.

The meter is labelled a guide, not a prediction.

### 9.6 Notifications
There is one optional weekly nudge ("New week: 3 sessions ready") on the officer's chosen day and time. Nothing guilt-based is ever sent.

---

## 10. Safety, privacy and trust

### 10.1 Health and safety
- A readiness questionnaire runs before the first run. It is modelled on PAR-Q style questions, with the final wording **[Jon to confirm]**.
- A persistent health disclaimer says FIV4RS is not medical advice.
- Warm-up blocks are included by default.
- The runner shows "Stop if you feel chest pain, dizziness or unwell" before each run.
- **Pause my plan** (for injury, illness or leave) freezes the plan without losing progress.

### 10.2 Privacy (UK GDPR)
- Fitness and health-adjacent data is treated as sensitive. There is a clear privacy notice, a lawful basis, and explicit consent for the safety questionnaire answers.
- Data is minimised: the name is optional until a coaching request, and there are no location or contacts permissions.
- Sharing with Jon is per request and per field, and every share is recorded. Withdrawing consent stops future sharing.
- In-app **Export my data** (JSON) and **Delete my account**, which hard-deletes the account and its data within 30 days.
- Supabase Row Level Security: an officer can only read and write their own rows. Jon's future access comes only through a `coach_link` scope.
- Chris must confirm the data controller identity (Chris, a company, or joint with Enforcer Fitness) before launch.

---

## 11. Architecture

```text
apps/mobile          Expo app (Expo Router, TypeScript)
packages/core        Pure TS: shuttle engine, plan builder, readiness, content schema (zod). No React
packages/content     Default content pack JSON plus schema tests
supabase/            Migrations, RLS policies, seed data, edge functions, RLS tests
docs/                Specs, plans, Jon's questions pack
```

- **Stack:** Expo SDK 57, React Native, TypeScript, Expo Router, npm workspaces and vitest, matching Chris's other projects for familiarity. There is no shared code or branding with SHFTRS.
- **Local-first data:** expo-sqlite holds sessions, runs and the plan. A sync module pushes pending rows to Supabase when online and pulls on sign-in. On conflict the newest edit wins for profile fields. Runs and session logs are append-only, so they never conflict.
- **Auth:** Supabase email and password. Tokens are stored in expo-secure-store on native.
- **Email to Jon:** a Supabase Edge Function triggered by a new `coaching_request` sends Jon a summary through a transactional email provider (Resend by default). The email includes a signed link for Jon to mark the request "replied".
- **Environments:** local Supabase (Docker) for development and CI. A hosted project is needed only at beta (Chris's free slots are in use; pause SHFTRS Test or go Pro then).

### 11.1 Data model (v1)
| Table | Key columns | Notes |
|---|---|---|
| `profiles` | user_id, display_name?, test_date?, sessions_per_week, activity_level, starting_level, plan_paused_at? | One per user |
| `safety_checks` | user_id, answers (jsonb), any_flagged, acknowledged_at | Explicit consent recorded |
| `content_packs` | id, version, source, author_id?, body (jsonb), published_at | Read-only to officers |
| `plans` | user_id, content_pack_id, inputs (jsonb), weeks (jsonb), built_at | Rebuilt on input change; previous plans kept |
| `session_logs` | id, user_id, session_id, started_at, ended_at, blocks_done (jsonb), effort?, note?, short_version | Append-only |
| `shuttle_runs` | id, user_id, mode, distance_m, level_reached, shuttles, duration_ms, started_at | Append-only |
| `test_results` | user_id, test_date, outcome: passed / not_this_time / didnt_take | Drives the §6 moments |
| `coaching_requests` | id, user_id, coach_id, goal, shared (jsonb snapshot of only the toggled fields), note, status, created_at | Referral count source |
| `coaches` | id, name, brand, contact_email, active | Seeded with Jon / Enforcer Fitness |
| `coach_links` | user_id, coach_id, scope (jsonb), consent_at, revoked_at? | Created now, unused in v1 |

Every table has RLS. There are SQL tests proving that an officer cannot read another officer's rows, and that coaches see nothing without an active link.

---

## 12. Brand and UI

- **Palette:** charcoal `#151719` (background), raised panels about `#1E2124`, off-white `#F5F4EC` (text), muted `#A6A9A3`, volt `#D4FF3F` (the single accent, used for progress, primary buttons and the 4). Success uses volt. Errors use a soft coral `#FF8A7A`, used sparingly and never for "missed".
- **Type:** the logo stays as artwork. For in-app headings and the huge level numerals, use an open-licence heavy condensed italic close to the logo (Barlow Condensed ExtraBold Italic by default). Use Inter for body text. Adelle Sans is only used in the app if Chris's licence covers app embedding.
- **Contrast:** all text meets WCAG AA on charcoal. The runner screen is readable at arm's length in daylight, with a minimum 96 pt level readout.
- **Accessibility:** Dynamic Type, VoiceOver and TalkBack labels, and haptic plus visual cues alongside audio. Listen-and-learn works with captions.
- **Enforcer Fitness:** the white wordmark appears small on Welcome, the Coach tab and the Share with Jon sheet. It never competes with the FIV4RS logo.

---

## 13. Error handling and edge cases
- **No signal:** everything works offline. Coaching requests queue and send when back online, with a visible "Will send when you're back online".
- **Audio interrupted** (phone call or other audio): the runner pauses and the officer chooses **Resume** or **End**. A partial run is logged with an "interrupted" flag.
- **Test date in the past:** the app asks for the result (§6.1) and does not silently rebuild.
- **Content pack fails validation:** the app keeps the last good pack, and the error is logged for Chris.
- **Sync failure:** retries with backoff. Nothing is ever lost locally.

---

## 14. Testing and quality
- **Core (vitest, test-first):** the shuttle schedule matches the §4 table exactly, plus 10m scaling and interval modes. The plan builder is covered for every runway length, no date, a date change, a pause and adaptation. The readiness formula and the content schema are validated.
- **Database:** RLS tests with `supabase test db`, plus migration checks in CI.
- **App:** typecheck, a component test for each tab, and a web build smoke test in CI.
- **On-device checklist** before each release: beep accuracy over a full 3:35 run (recorded and checked against the schedule), audio with the screen locked on iOS and Android, Bluetooth headphones, a phone-call interruption, and offline then sync.
- **Content sign-off:** a CI check fails while any **[Jon to confirm]** markers remain in the shipping content pack.

---

## 15. Open items
| Item | Owner |
|---|---|
| All training defaults in this spec, consolidated in `docs/jon/questions-for-jon.md` | Jon |
| Missed-bleep warning rules and specialist role levels (verify against College of Policing) | Jon / Chris |
| Data controller identity and privacy notice | Chris |
| Referral share terms with Jon | Chris and Jon |
| Adelle Sans licence scope (logo only, or app embedding too) | Chris |
| Hosted Supabase at beta (pause SHFTRS Test or upgrade to Pro) | Chris |
| Transactional email provider account (Resend default) | Chris |

---

## 16. Next: the coaching platform (separate project)
Out of scope for v1, recorded so v1 stays compatible. Coaches get a web dashboard to see linked officers (within consent scope), assign programmes (content packs with `source = coach`), message, and track check-ins. Coaches pay a monthly subscription. Jon is the first coach and it replaces Everfit for him. The officer side is FIV4RS itself.
