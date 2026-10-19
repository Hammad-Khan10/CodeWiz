# Code Gigs Plan

## What the rules ask for

- At least two code gigs published on Devmarketplace during the build, not all at the end
- Each one is code we wrote during the event, lifted out of our project
- Each has a clear title, a short description, and setup instructions another developer can follow
- Every teammate is credited as a contributor
- Only code we wrote gets published. Open-source pieces are linked under their own licences, not copied
- Gigs are reviewed as part of judging

## Our plan: two required, up to three bonus, published early

| Gig | Built from | Publish by | Why another developer would want it |
|---|---|---|---|
| 1. GitHub Repo Indexer | IMPORT and PARSE tasks | Tuesday | Turns any public repo into functions, a call graph and metrics, for code search, docs or review tools |
| 2. Verified Bug Planter | DRILL-1 to DRILL-3 | Wednesday | Generates realistic bugs with a checked answer key, for training, testing reviewers or testing test suites |
| 3. Supabase TOTP 2FA for Next.js (bonus) | AUTH-1 to AUTH-3 | Monday night or Tuesday | 2FA is the most common reason hackathon projects fail, and every Next.js and Supabase app needs it |
| 4. Typed LLM JSON Calls (bonus) | AI-1 | Wednesday | One function for schema-checked JSON from OpenAI or Anthropic, with retry |
| 5. Package Registry Verifier (bonus) | LIB-2 | Thursday | Stops AI from recommending packages or functions that don't exist |

Two good gigs beat five rushed ones. Get 1 and 2 out on time first.

## How to build code so it lifts out cleanly

Write each gig's code this way from the start, not as a clean-up at the end.

- **Its own folder** under `lib/` (for example `lib/repo-indexer/`), with one `index.ts` that exports a small public API.
- **No app imports.** Nothing from `app/`, `components/`, `lib/supabase/` or our database types. Pass in what it needs: tokens, an LLM call function, limits.
- **Typed inputs and outputs.** Export the types so users know exactly what goes in and what comes out.
- **No secrets or hard-coded URLs.** Everything is configurable, with sensible defaults.
- **Errors as results.** Return `{ ok: false, reason }` rather than throwing raw strings.
- **A README in the folder:** what it does, install, setup, a short usage example, limits, dependencies with licences, contributors.
- **One example or test** that shows it working.
- **Framework-free, unless the gig is about the framework.** The 2FA gig is Next.js-specific by design. The indexer and the planter shouldn't be.

## Publishing checklist (every gig)

- [ ] A title that says what it does and is easy to search
- [ ] A 2 to 3 sentence description: the problem, what it does, the stack
- [ ] Setup steps another developer can follow from zero
- [ ] A usage example
- [ ] Dependencies listed with licences and links
- [ ] All four teammates credited: Roshaan Tahir, M Hammad Khan, Fizzah Abdullah, Hammad Khan
- [ ] Matches the code in our repo, with a link to the commit
- [ ] Link added to our Devpost submission

## Gig specs

### 1. GitHub Repo Indexer

**Title idea:** GitHub Repo Indexer: functions, call graph and metrics for any public repo

**Description draft:** Point it at a public GitHub repo and get back every Python, JavaScript and TypeScript function with exact line ranges, an approximate call graph and simple metrics. It uses the GitHub API and tree-sitter, with no cloning and no code execution.

**Public API:** `indexRepo(options)` returns the repo's owner, name and commit SHA, plus `files`, `functions`, `calls`, `skipped` and a `truncated` flag.
- Options: `url`, `token`, an optional `folder`, and limits (max files, max file size, skip patterns, languages)
- Each function: `id`, `file`, `name`, `qualifiedName`, `language`, `startLine`, `endLine`, `code`, metrics (`length`, `branches`, `callers`, `callees`, `dataAccess`), `category`, `importance`

**Done when:** someone can index all three sample repos from a clean install using only its README.

### 2. Verified Bug Planter

**Title idea:** Verified Bug Planter: realistic, checked bugs for code review practice

**Description draft:** Give it a function and get back a copy with 1 to 3 realistic bugs and an answer key. Every bug is checked: the diff must match the key, the code must still parse, and a second pass confirms the behaviour changes. Bring your own LLM.

**Public API:** `plantBugs(options)` returns either `{ ok: true, changedCode, bugs }` or `{ ok: false, reason }`.
- Options: `code`, `language`, `startLine`, `count`, allowed `bugTypes`, an `llm` function that takes a prompt and a schema and returns JSON, and an optional `parse` function for the syntax check
- Each bug: `lineStart`, `lineEnd`, `bugType`, `explanation`, `fix`

**Done when:** it works with both OpenAI and Anthropic by swapping only the `llm` function.

### 3. Supabase TOTP 2FA for Next.js (bonus)

**Includes:** an enrolment page with a QR code, a code entry page, the middleware or proxy guard that requires AAL2, a server-side check for API routes, an example access-rule policy that requires 2FA, and the profile trigger.

**Done when:** a fresh Next.js app gets working 2FA by following the README.

### 4. Typed LLM JSON Calls (bonus)

**Public API:** `callJson({ prompt, schema, provider, model })` returns validated data, or a clean error after one retry.

### 5. Package Registry Verifier (bonus)

**Public API:** `verifyPackage({ ecosystem, package, functionName })` returns whether the package exists, whether the function name appears in its README or description, and the docs link.

## Timing

- Publish as soon as a gig works, then update it as it improves. Don't wait for polish.
- If the folder rules above are followed from the start, packaging a gig should take about an hour.
