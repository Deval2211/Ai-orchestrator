# Phase 4: Browser

**Status:** planned later phase; implementation not started

## Goal

Support one sanctioned browser-based AI workflow through a dedicated manually authenticated Chrome profile, without CAPTCHA bypass, credential extraction or quota evasion.

## Work items

### 4.1 Profile management

- [ ] Register dedicated profile paths and permitted services.
- [ ] Launch/connect through Hermes browser/CDP tooling or a supported automation framework.
- [ ] Track process/profile ownership and clean up only owned resources.
- [ ] Represent authentication as observed state: unknown, authenticated, expired or manual action required.

### 4.2 Browser adapter

- [ ] Implement side-effect-free status checks and explicit connection/reconnection behavior.
- [ ] Validate service eligibility, automation terms, privacy and data destination before planning.
- [ ] Emit normalized browser/action/result/error events and bounded redacted artifacts.
- [ ] Pause for manual login or verification; never auto-solve CAPTCHA or anti-bot challenges.
- [ ] Prefer an official API whenever it supplies equivalent capability.

### 4.3 Session and privacy handling

- [ ] Keep cookies/tokens inside the provider-owned profile; never copy them into registry, plans, logs or context bundles.
- [ ] Use one dedicated process/profile per account and prevent accidental profile sharing.
- [ ] Revalidate authentication and permitted action scope before each execution.
- [ ] Record exactly what external action occurred and verify the destination/result.

## Deliverable

From Hermes, run one approved prompt/workflow through a permitted browser-AI service and retrieve the result and execution evidence. A service that requires manual verification pauses cleanly for the user.

## Acceptance criteria

- [ ] A dedicated profile completes one real, permitted workflow.
- [ ] Another profile cannot observe its authenticated state or cookies.
- [ ] Login/CAPTCHA produces `authentication_required`, not an evasion attempt.
- [ ] Browser artifacts contain no cookies, tokens or unauthorized page data.
- [ ] Disconnect/reconnect does not duplicate a consequential action.
- [ ] An equivalent official API, when available, is selected instead of the browser path.

## Explicitly prohibited

CAPTCHA solving, anti-bot evasion, cookie/OAuth extraction, unauthorized consumer-web bridging, and account rotation to defeat quotas.
