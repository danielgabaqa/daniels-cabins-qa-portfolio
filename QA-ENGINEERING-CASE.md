# QA Engineering Case Study: Daniel's Cabins

## Scope

Daniel's Cabins is a private Expo/React Native app used by two managers of a two-cabin hospitality business. The product handles guest inquiries, reservations, dates and availability, pricing, discounts, breakfast requests, late checkout, archives, and manager activity.

This public repository contains documentation only. It has no app source, SQL, environment configuration, credentials, guest data, screenshots, or installable build.

## Risks Under Test

| Risk | Why it matters | Test oracle |
|---|---|---|
| Same-cabin date overlap | Causes double-booking | Overlapping interval is rejected; next stay on checkout date is allowed |
| Wrong cabin association | Shows or blocks dates on the wrong property | Same range is accepted for the other cabin |
| Incorrect price | Financial loss or guest dispute | Assert calculated total and persisted payload for weekday/weekend, discount, and manual override |
| Invalid guest data | Incomplete reservation | Missing name/phone leaves form open and writes no booking |
| Stale calendar state | User selects invalid checkout after changing check-in | Earlier check-in clears previous checkout and requires a new end date |
| Guest-data exposure | Privacy and compliance risk | Separate authenticated non-manager must not read or insert guest rows under RLS |

## Browser Test Architecture

The private source has 34 Playwright browser scenarios, run in Chromium against the real Expo web UI:

- `booking-cases.spec.js`: 27 cases for source attribution, manual price, five discount values, required fields, invalid same-day range, weekend rate, negative amount, overlap, checkout adjacency, cabin isolation, cancelled/archived records, busy-day markers, breakfast variants, and late checkout.
- `booking-ui.spec.js`: 4 cases for cabin/date prerequisites, changing check-in, source/discount interaction, and busy calendar rendering.
- `booking-workflows.spec.js`: 2 cases for creating a booking and editing an existing booking without duplicating it.
- `login.spec.js`: 1 login-screen smoke check.

`fixtures/mockSupabase.js` intercepts the app's Supabase Auth token and PostgREST endpoints. It returns a deterministic synthetic manager session, two cabins, configurable seeded records, and stores writes in memory. Playwright resets the browser context and fixture state for each case. The browser clock is fixed to make date scenarios repeatable.

**What this proves:** rendered controls, user interactions, validation, date decisions, calculated totals, and app-generated data writes against a controlled API contract.

**What this does not prove:** actual Supabase authentication, database SQL constraints, row-level security, realtime synchronization, push delivery, or native iOS behavior.

## Concrete Regression Evidence

1. **Earlier check-in:** reproduces a stale-checkout defect; verifies a new checkout must be selected after moving the start earlier.
2. **Overlap boundaries:** rejects overlapping dates for the same cabin, permits a new stay on the prior checkout day, and permits the same dates on the other cabin.
3. **Availability state:** cancelled and archived bookings do not block dates; active booking check-in and final occupied night carry calendar period markers.
4. **Pricing:** checks 0%, 5%, 10%, 15%, and 20% discounts against exact totals, a weekend tariff, a manual override, and rejection of a negative price.
5. **Breakfast:** validates visibility, persisted paid request, free gift, multiple serving rows, and required paid price.
6. **Persistence:** creates a guest and linked booking; edits a seeded reservation and confirms the existing ID remains unchanged.

Four Node unit tests separately check date inclusion/exclusion, overlap, cancelled-booking handling, and ignoring a booking's own ID during edit.

## CI and Local Evidence

GitHub Actions quality checks run typecheck, unit tests, Expo SDK compatibility, iOS/Android/web bundle exports, and the credential-free Playwright suite. The run uploads the Playwright HTML report and failure artifacts.

Locally verified: 34/34 Playwright browser cases, 4/4 unit tests, TypeScript, Expo dependency check, iOS/Android/web bundle exports, and workflow YAML syntax.

## Backend Security Tests: Pending Execution

Two separate `@backend` cases are prepared for a disposable Supabase project:

1. Authenticate as a manager, create a synthetic inquiry, and verify it is returned in the app.
2. Authenticate as a manager and a separate confirmed non-manager. Create a synthetic guest as the manager, verify the other account cannot read it, attempt a guest insert as that account, and delete the fixture.

These tests have **not passed yet**. Earlier GitHub attempts exposed mismatched secret names and, afterward, an old permissive RLS policy in the test project. The workflows now map to the configured Supabase URL/key names, and `supabase-rls.sql` was updated to remove previous policies on app-owned tables before creating the manager allowlist. That latest SQL still must be applied to the test project and the backend workflow rerun before claiming RLS is verified.

Do not use production guest data or production credentials. The backend workflow requires six repository secrets for a disposable test project and an explicit confirmation input.

## Native Test Boundary

A Maestro inquiry flow is prepared for iOS Simulator but has not been run here; this environment lacks Xcode Simulator. The Playwright suite is browser E2E, not native-device E2E.

## Portfolio Statement

> QA automation portfolio project for a private hospitality app: 34 locally passing Playwright browser cases, four business-rule unit tests, deterministic in-memory API fixtures, cross-platform CI checks, and separately prepared Supabase RLS tests. Live backend and native results are explicitly tracked as pending rather than presented as passed.
