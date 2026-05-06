# Testing Requirements

## Coverage: 80% minimum

All required: **Unit** (functions, components) + **Integration** (API, DB) + **E2E** (Playwright critical flows).

## MANDATORY: Browser Walkthrough Before Claiming Done

Unit tests passing is NOT sufficient. Before ANY feature is complete:
1. Walk the FULL user journey in the browser — every click, every page transition
2. Verify data persists and cross-page flows work
3. Check empty/error states in the actual UI
4. User should NEVER be the one to find broken flows

Common gaps browser testing catches: DB constraint violations, cross-component state issues, buttons to unready pages, display-without-persist bugs.

## TDD Workflow

RED (write failing test) → GREEN (minimal implementation) → IMPROVE (refactor) → verify 80%+ coverage.
