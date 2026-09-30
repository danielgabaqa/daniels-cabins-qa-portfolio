# QA Automation Case Study

## Daniel's Cabins

**Product:** Private operations app for two hospitality managers and two guest cabins.  
**Stack:** Expo/React Native, TypeScript, Supabase, Playwright, Node test runner, GitHub Actions.

### Risk Model

The highest-impact failures are overlapping bookings, incorrect prices, lost guest details, and unauthorized access to guest records. The test strategy separates deterministic business/UI checks from live-backend security checks so mocked coverage is not confused with integration evidence.

### Coverage

**Unit tests (4):** check-in/check-out boundaries, same-cabin overlap, independent cabins, cancellation, and editing an existing booking.

**Playwright UI (34):** login and manager choices; booking form defaults; source selection; required-field validation; same-day range rejection; price/discount calculations; manual price; weekend pricing; overlapping and adjacent stays; cabin separation; cancelled and archived bookings; busy calendar markers; breakfast pricing, gift, and quantity; late checkout; create and edit persistence within a seeded in-memory API.

**CI:** typecheck, unit tests, Expo dependency validation, iOS/Android/web bundle exports, and credential-free Playwright scenarios run on pushes and pull requests. Playwright HTML reports and failure artifacts are uploaded by the workflow.

**Backend integration (prepared, not yet passed):** a manual Playwright workflow signs in against a dedicated Supabase test project, creates a synthetic inquiry, and verifies RLS prevents a separately authenticated non-manager from reading or inserting guest data. It requires six test-only GitHub secrets and explicit test-backend confirmation. Do not point it at production.

**Native coverage (prepared, not yet passed):** a Maestro iOS Simulator inquiry flow exists separately. It requires Xcode Simulator and a dedicated test backend.

### Defects Found Through Testing

1. The dashboard's new-inquiry action appeared absent because white text was on a white secondary surface. Contrast and iconography were corrected; a stable test ID supports regression coverage.
2. Moving check-in earlier retained the old checkout date. The state transition now clears checkout, with a browser regression test covering selection of the new range.

### Evidence and Limits

Local verification: 34/34 credential-free Playwright cases, 4/4 unit tests, TypeScript check, Expo dependency check, native/web bundle exports, and workflow YAML parsing.

Not verified against a live backend: Supabase sign-in, persisted inquiry E2E, RLS enforcement, and native notification delivery. RLS SQL is present but must be applied and exercised in an isolated test project before claiming those checks pass.

### Interview Walkthrough

1. Explain the guest-data and double-booking risks.
2. Show a new booking and an edit in the browser.
3. Open the calendar and discuss the busy/adjacent-date boundary cases.
4. Run Playwright headed and show the HTML report.
5. Explain the test double and why it protects production data.
6. Distinguish passed mocked tests from pending Supabase/RLS integration evidence.

### CV Bullet

> Designed a risk-based QA approach for a private hospitality application, adding 34 Playwright browser scenarios, four business-rule unit tests, GitHub Actions quality checks, calendar regression coverage, and prepared manager-scoped Supabase RLS integration tests.
