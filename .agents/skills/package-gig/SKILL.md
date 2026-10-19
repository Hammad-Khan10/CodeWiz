---
name: package-gig
description: Use when preparing a lib/ folder in CodeWiz to publish as a Devmarketplace code gig, or when checking whether a gig folder is ready to publish.
---

# Package a code gig

The rules and our list of gigs are in `docs/GIGS.md`. Devmarketplace reviews gigs as part of judging.

## Steps

1. Check that the folder imports nothing from `app/`, `components/`, `lib/supabase/`, `lib/server/` or database types. Search the imports, and fix any by passing values in as parameters.
2. Make sure `index.ts` exports only the public API and its types.
3. Make sure there's at least one test or example that runs without secrets.
4. Write or update `README.md` in the folder:
   - Title: says what it does and is easy to search
   - Description: 2 to 3 sentences on the problem, what it does and the stack
   - Install and setup, starting from zero
   - A short usage example
   - Options and limits
   - Dependencies with licences and links
   - Contributors: Roshaan Tahir, M Hammad Khan, Fizzah Abdullah, Hammad Khan
5. Publish only our own code. Link open-source dependencies, and never paste their code in.
6. Note the commit hash the gig was published from.

## Check before publishing

- [ ] A developer could use it from the README alone
- [ ] No secrets, tokens or hard-coded URLs
- [ ] The tests or example pass
- [ ] The gig link is added to the Devpost submission draft
