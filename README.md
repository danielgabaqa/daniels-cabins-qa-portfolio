# Daniel's Cabins | QA Automation Portfolio

This is a **documentation-only** portfolio for a private hospitality-management app. It contains no app source, database scripts, credentials, guest data, screenshots, or downloadable build.

## Verifiable Evidence

- **34 Playwright browser cases passed locally** against an in-memory Supabase API fixture.
- **4 Node unit tests passed** for stay-date boundaries and booking-overlap rules.
- **CI is configured** for typechecking, unit tests, Expo package checks, iOS/Android/web bundle exports, and the browser suite.
- **Two real-Supabase backend cases are prepared but not passed:** inquiry persistence and RLS denial for a non-manager. The latest attempted run exposed an old permissive policy in the isolated database; updated RLS SQL must be applied before rerunning.
- **Native iOS Maestro flow is prepared, not run:** no iOS Simulator was available in the local environment.

## Read the Evidence

- [Technical case study: risks, harness, defects, verification limits](QA-ENGINEERING-CASE.md)
- [34-case Playwright scenario matrix](TEST-MATRIX-DETAILS.md)

## CV Wording

> QA automation portfolio project for a private hospitality app: 34 locally passing Playwright browser scenarios, four business-rule unit tests, deterministic in-memory API fixtures, and cross-platform CI checks. Separate Supabase/RLS integration tests are prepared; live-backend verification remains pending.

Source and real guest data remain private. A sanitized walkthrough can be shared separately.
