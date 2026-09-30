# Playwright Browser Test Matrix

**Total:** 34 passed locally in Chromium  
**Target:** Expo app rendered on React Native Web  
**Backend:** deterministic in-memory Supabase/Auth/PostgREST fixture  
**Clock:** fixed to September 15, 2026 for repeatable calendar cases

Each row below corresponds to a Playwright `test()` case in the private application repository.

| # | Scenario | Action / seeded condition | Expected assertion |
|---:|---|---|---|
| 1 | Login screen smoke | Open the app | Brand, both managers, PIN input, and sign-in control are visible |
| 2 | Cabin prerequisite | Open booking form | Date control disabled until a cabin is selected |
| 3 | Earlier check-in reset | Move check-in earlier than default | Old checkout is cleared; a replacement checkout completes the range |
| 4 | Source and discount controls | Toggle Direct / Booking.com; choose 20% | Controls react and selected state updates |
| 5 | Busy calendar from another booking | Seed an active stay and open calendar | Busy range is visibly marked |
| 6 | Direct creator attribution | Save a direct booking | Saved source and creator are Daniel |
| 7 | Booking.com source | Save an OTA booking | `Booking.com` source persists |
| 8 | Manual total override | Enter 1234 | Displayed total and saved booking total are 1234 |
| 9 | Zero discount | Select 0% | Total remains 400 |
| 10 | Five-percent discount | Select 5% | Total is 380 |
| 11 | Ten-percent discount | Select 10% | Total is 360 |
| 12 | Fifteen-percent discount | Select 15% | Total is 340 |
| 13 | Twenty-percent discount | Select 20% | Total is 320 |
| 14 | Cabin is required | Submit with no cabin | Form remains open; no booking saved |
| 15 | Guest name required | Submit without name | Form remains open; no booking saved |
| 16 | Guest phone required | Submit without phone | Form remains open; no booking saved |
| 17 | Both guest fields required | Submit empty guest section | Form remains open; no booking saved |
| 18 | Same-day stay rejected | Set check-in equal to checkout | Invalid stay rejected; no booking saved |
| 19 | Weekend tariff | Select Thursday-to-Friday | Weekend price 600 is applied |
| 20 | Negative manual amount | Enter -1 | Invalid price rejected; no booking saved |
| 21 | Same-cabin overlap | Seed active overlap | Conflicting range rejected in the calendar flow |
| 22 | Checkout is exclusive | New check-in equals existing checkout | Adjacent stay is accepted |
| 23 | Cabin isolation | Same dates, other cabin | Booking in the other cabin is accepted |
| 24 | Cancelled stay | Seed cancelled overlap | Dates remain available |
| 25 | Archived stay | Seed archived overlap | Dates remain available |
| 26 | Busy period endpoints | Seed a multi-night active stay | Check-in and last occupied night expose period-start/end markers |
| 27 | Breakfast fields reveal | Enable breakfast | Price and serving-date controls appear |
| 28 | Paid breakfast persists | Save a breakfast costing 125 | Breakfast row links to booking and stores price 125 |
| 29 | Gift breakfast | Enable gift breakfast | Price field hidden; saved gift costs 0 |
| 30 | Breakfast quantity | Select three servings | Three date rows and serving-time controls appear |
| 31 | Paid breakfast price required | Enable paid breakfast without price | Booking is not saved |
| 32 | Late checkout default | Enable late checkout | Defaults to and persists 14:00 |
| 33 | Create workflow | Save new guest and booking | Client is created and booking links to that client/cabin |
| 34 | Edit workflow | Change guest on seeded booking | Existing booking ID remains; client updates; no duplicate booking |

## Test Harness Mechanics

For each test, the Playwright fixture creates a fresh browser context, fixes the browser clock, intercepts the Supabase Auth token request, and intercepts PostgREST requests. It returns a synthetic manager session and seeded cabins, clients, bookings, breakfast rows, and rates. Writes update in-memory state, which the test inspects after the UI action.

This checks browser UI behavior and the app's request/payload handling. It does **not** contact Supabase or prove credentials, SQL constraints, RLS, realtime, push, or native iOS behavior.

## Separate Backend Tests

Two `@backend` tests are not included in the 34 passing cases:

1. Save a synthetic inquiry to an isolated Supabase project and verify it appears in the app.
2. Sign in as a manager and a separate authenticated non-manager; verify RLS hides the manager-created guest and rejects a guest insert from the non-manager.

They are configured but have **not passed yet**. A recent run found an old permissive RLS policy in the test project. The updated `supabase-rls.sql` removes existing policies on app-owned tables before recreating the manager allowlist. Apply that SQL to the isolated test project before rerunning. Never use production data or credentials.
