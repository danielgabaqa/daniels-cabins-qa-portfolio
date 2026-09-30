# Daniel's Cabins | QA Automation Case Study

A documentation-only portfolio case study for a private bilingual hospitality operations app. This repository intentionally contains no application source, database scripts, credentials, guest data, screenshots, or downloadable build.

## Project

Daniel's Cabins supports two managers coordinating reservations, guest inquiries, availability for two cabins, pricing, breakfast, late checkout, archives, reporting, and collaboration. The app is built with Expo/React Native and Supabase, with Hebrew and English interfaces.

## QA Work

- **34 Playwright browser scenarios** cover login, booking creation/editing, required fields, pricing and discounts, calendar boundaries and conflicts, cabin separation, archived/cancelled stays, breakfast, and late checkout. These run against a deterministic in-memory Supabase test double and do not touch production data.
- **4 unit tests** cover stay-date boundaries, overlap rules, cancellation, and editing an existing booking.
- **GitHub Actions CI** checks types, unit tests, Expo dependency compatibility, iOS/Android/web bundle exports, and the credential-free Playwright suite.
- **2 isolated-backend Playwright tests are prepared**: inquiry persistence and manager-vs-non-manager guest-data access. They are not claimed as passed because the isolated Supabase test project and test accounts have not yet been configured.
- **Native iOS Maestro flow** is prepared separately; it has not been run in this environment because an iOS Simulator was unavailable.

## Example Defect

A dashboard action looked absent because its white label was placed on a white secondary button. The contrast and affordance were corrected and a browser regression assertion was added. Calendar testing also exposed a stale-checkout issue after moving check-in earlier; the state transition was fixed and covered by a regression case.

## Verification Boundary

The 34 browser scenarios validate real UI interactions with seeded mock API responses. They do not validate production Supabase authentication, live row-level security, push delivery, or native iOS behavior. The live Supabase/RLS cases require a disposable test project and dedicated manager/non-manager accounts. Never use guest data or production credentials for E2E.

## CV Summary

> Built a risk-based QA suite for a private bilingual hospitality app: 34 Playwright browser scenarios, four business-rule unit tests, cross-platform CI bundle checks, and prepared Supabase RLS integration coverage. Used seeded test data to protect production records and documented unverified integration boundaries.

For a sanitized walkthrough, contact the project owner. No app download or source code is provided here.
