# CodeWiz Implementation Plan

How we get from an empty repo at 9 AM Monday to a deployed, submitted app by 8 PM Thursday.

| Doc | What's in it |
|---|---|
| [DECISIONS.md](DECISIONS.md) | What we decided and why, so nobody re-argues it mid-build |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Repo layout, data model, access rules, API routes, AI contracts, scoring, limits |
| [TASKS.md](TASKS.md) | Everything we need to build, by module, with what to watch out for and when it's done |
| [GIGS.md](GIGS.md) | Which parts become Devmarketplace code gigs, and how to build them so they lift out cleanly |
| [QA_AND_SUBMISSION.md](QA_AND_SUBMISSION.md) | Test checklists, demo script, README and AI_DISCLOSURE outlines, submission checklist |

## What the judges told us at kickoff

- **Token resets count.** Each member gets one free AI token reset; any extra resets count against us. The token budget section in AGENTS.md is how we stretch them.
- **A finished MVP beats many half-built features.** Extras only start once the core journey is solid. If something has to go, Semgrep goes first.
- **AI readiness is judged.** AGENTS.md, the nested AGENTS.md files and the skills in `.agents/skills/` cover this. Update them after the organisers' guidance session.
- **At least one working AI feature is required.** Ours is the whole core loop.

## Ground rules

- **Declare anything pre-existing.** If code or assets from before kickoff end up in the product, list them in PRIOR_WORK.md. Declaring costs nothing; not declaring is a disqualification.
- **`main` on `RoshaanT1/CodeWiz` is always deployable.** Vercel deploys every merge to `main`.
- **Commit small and often, from your own GitHub account.** Judges check that commits are spread across 19 to 22 October.
- **Core before extras.** Extras start only once the core journey works on the live site.
- **AI output is untrusted until code checks it.** See the contracts in ARCHITECTURE.md.
- **Never run repo code.** Everything is static: parsing, diffs, registry lookups, Semgrep.

## Core vs extras

| Core (must ship) | Extras (only after the core is live) |
|---|---|
| Sign-up, sign-in, TOTP 2FA, per-user data isolation | Best practices and security (Semgrep) with "defend your choice" |
| Repo import, parsing, call map, importance ranking | Library check with registry verification |
| Function quiz with plain-language explanation | Code metrics panel |
| Bug drills, grading, real-bug check | |
| Understanding score, skill profile, history, coverage | |

The critical path is import → parse → rank and select → quiz → drill → grade → score. Auth and the database are built alongside it from the first hour. Anything not on this path can wait.

## Timeline

### Sunday 18: setup only, no code

- [ ] All four: Devmarketplace account created, Devpost registration confirmed, Discord joined, free for 9 AM Monday at NED
- [ ] Vercel project created and linked to the repo
- [ ] Supabase project created, email sign-in on, TOTP MFA enabled in the auth settings
- [ ] Email confirmation decided: off for the event, or a custom SMTP provider (see AUTH-1 in TASKS.md)
- [ ] GitHub fine-grained token with read-only access to public repositories
- [ ] Account on a container host for the Semgrep service (Railway, Render or Fly.io)
- [ ] Three small public sample repos picked: one Python, one JavaScript, one TypeScript
- [ ] Asked on Discord: do AI credits last until winners are announced, which models do they cover, and does the whole build have to happen in the Devmarketplace workspace?

### Monday 19: kickoff

**Goal by 1 PM:** a deployed app where you can sign up, sign in, paste a repo URL and see its files and functions.

| Time | Milestone |
|---|---|
| 9:00 to 9:30 | Briefing, problem sign-off, AI credits. Confirm the contracts in ARCHITECTURE.md |
| 9:30 to 11:00 | One person builds the skeleton in the main repo (Phase 1 below) and deploys it. Everyone else forks, clones and sets up their machine. Team lead creates the Devpost project |
| 11:00 | Skeleton merged. Everyone syncs their fork |
| 11:00 to 12:30 | In forks, in parallel: sign-up and sign-in pages, repo URL form and GitHub import, the first migration, the tree-sitter parse route |
| 12:30 | Merge window. Check everything works together on the live site |
| 12:45 to 1:00 | Show an organiser in the Devmarketplace workspace. Hand in PRIOR_WORK.md if needed |

If parsing isn't live by 12:45, show the file list. Never miss the checkpoint for a stretch goal.

**Monday afternoon and night**
- 2FA enrolment and challenge, with every app route requiring a 2FA session
- Full schema migration with access rules on every table
- AI wrapper with schema checks and retries, with one validated call working on the live site
- Function metrics, call edges and categories stored
- Bonus: package the 2FA setup as gig 3 while it's fresh

### Tuesday 20: repo map and quiz

- Ranking, AI selection with reasons, rounds of 3, 5 or 10
- Repo page with the call map and function list
- Round flow with progressive generation
- Quiz generation, answers, grading, explanation reveal
- **Publish gig 1** (GitHub Repo Indexer)

**Done when:** on the live site you can import a sample repo, start a round and finish the quiz for every function in it.

### Wednesday 21: drills and scores

- Bug planting with verification
- Drill screen with line comments
- Drill grading and the real-bug check
- Results page, understanding score, skill profile, history, coverage
- **Publish gig 2** (Verified Bug Planter)
- Two-account isolation test and restart test (see QA_AND_SUBMISSION.md)
- **Decide on extras:** if the core isn't solid tonight, drop Semgrep and spend Thursday on polish

**Done when:** the full core journey works end to end on the live site, twice in a row, in a private window.

### Thursday 22: extras, polish, submit

| Time | Milestone |
|---|---|
| Morning | Extras, each behind a feature flag: Semgrep suggestions, library check, metrics panel |
| Afternoon | Polish (loading, empty and error states, no placeholder text), README, AI_DISCLOSURE, demo video |
| 6 PM | Feature freeze. Only fixes from here |
| 8 PM | Submit on Devpost |
| 11 PM | Code freeze and Devpost closes. No commits after this |

## How we work in parallel: forks

### Phase 1: skeleton (one person, in the main repo)

Ideally whoever owns the frontend and platform. Merge it before anyone starts feature work.

- Next.js scaffolded with shadcn/ui, deployed to Vercel from `main`
- The folder structure from ARCHITECTURE.md, with empty `index.ts` files
- Every planned dependency installed once: zod, `@supabase/ssr`, `@supabase/supabase-js`, octokit, web-tree-sitter, diff, shiki, `@xyflow/react`, vitest, server-only. This avoids lockfile conflicts later
- The Supabase clients, plus stub helpers in `lib/server/`
- Route stubs in `app/api/` that return mock data in the agreed shapes, so screens can be built straight away
- `.env.example` and the lint, type check and test scripts, with the commands table in AGENTS.md updated to match

`create-next-app` may refuse to run in a folder that already has files. If it does, scaffold in a temporary folder and copy the files into the repo. Don't delete the docs.

### Phase 2: fork setup (everyone, about 10 minutes)

```
# Fork RoshaanT1/CodeWiz on GitHub, then:
git clone https://github.com/<your-username>/CodeWiz.git
cd CodeWiz
git remote add upstream https://github.com/RoshaanT1/CodeWiz.git
npm install
# Copy .env.example to .env.local and fill it from the private team chat
```

### Phase 3: the loop for every task

```
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
git checkout -b QUIZ-2-grade-answers
# work, commit often
git fetch upstream && git merge upstream/main   # just before opening the PR
git push origin QUIZ-2-grade-answers
# open a pull request: your fork's branch → RoshaanT1/CodeWiz main
```

### Folder ownership

The owner is the only person who edits a folder; everyone else asks. This is what keeps merge conflicts rare.

| Folder | Owner |
|---|---|
| `app/` pages, `components/` | Fizzah Abdullah |
| `lib/repo-indexer/`, `lib/server/`, `supabase/` | M Hammad Khan |
| `lib/ai/`, `lib/bug-planter/`, `lib/grading/`, `lib/scoring/` | Roshaan Tahir |
| `docs/`, `README.md`, `AI_DISCLOSURE.md` | Hammad Khan |
| `app/api/` | Each route belongs to whoever owns the logic it calls |

### Shared files

These are where conflicts happen. Each change gets its own small pull request, announced in the team chat:
- `package.json` and the lockfile (everything planned is installed in the skeleton)
- Shared types and zod schemas used across folders
- Migrations (only M Hammad creates and applies them)
- The root layout and navigation (only Fizzah)
- AGENTS.md

### Merging

- Only the repo owner can merge pull requests from forks, unless a teammate is added as a collaborator. Merge at fixed times each day (12:30, 4, 8 and 11 PM), or make one teammate a collaborator so merging never waits on one person.
- Use "Create a merge commit", not squash, so everyone's commits and dates stay in the history.
- Vercel may hold preview builds from forks until the owner approves them, so test locally before opening a pull request.
- After each merge window, everyone syncs their fork.

### Day to day

- Every task in TASKS.md becomes a GitHub issue on the main repo. Assign yourself before starting.
- One task in progress per person. Finish it, get it merged, then claim the next.
- Build against the contracts in ARCHITECTURE.md, using mock data with the same shapes so screens don't wait for the backend.
- A 10-minute sync each morning and evening: what's merged, what's blocked, what's next.
- "Done" means merged and working on the live site, not "works on my machine".
- Coding agents pick up AGENTS.md automatically. Give them the task ID, review everything they write, and note them in AI_DISCLOSURE.md.

## Definition of done for every task

- Merged, and working on the deployed site
- Has loading, empty and error states
- No secrets in browser code, and access rules respected
- Passes lint, type check, tests and build
- If it's part of a gig, its folder imports nothing from app code (see GIGS.md)

## Risks to check every day

| Risk | What to do |
|---|---|
| Vercel time limits on long AI steps | One request per step and per function; set `maxDuration` within the plan's limit |
| tree-sitter not loading on Vercel | Test the deployed parse route on Monday, and fix bundling before moving on |
| AI tokens or credits run out | Follow the token budget in AGENTS.md, keep the daily round cap, and confirm credit duration with the organisers |
| 2FA or data isolation breaks | Two-account test on Wednesday, then again before submitting |
| Merge conflicts | Folder ownership, small pull requests, syncing after every merge window |
| Merging waits on one person | Fixed merge windows, or a second collaborator who can merge |
| Scope creep | New ideas go into a "Later" issue, not into the build |
