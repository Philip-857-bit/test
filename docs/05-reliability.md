# Reliability and evaluation plan

## What is being proved

Correctness means the intended customer accepted a suitable appointment, the right event exists once, the correct waiting-list row is updated, and outgoing claims match observed state. A successful API status or a fluent model answer alone is insufficient.

All scenarios below are planned. There are currently no measured pass rates, timings, cost figures, or successful live runs.

## Evaluation layers

1. Deterministic policy tests: matching, duration, timezone, expiry, and identity checks using fixed inputs.
2. Workflow tests: fake adapters with persistent state and injected failures. Verify side effects and restart behavior. These are simulations, not live app evidence.
3. Model cases: real model outputs against authored expected decisions for messy cancellation and reply language. Record model ID and prompt version; repeat important cases to expose variability.
4. Live integration smoke run: controlled real Gmail, Calendar, Sheets, and Telegram accounts.
5. Live end-to-end demonstration: real external writes with a clearly labelled local interruption after Calendar accepts a booking.

Do not imply that a fault injected in the local adapter is a real Google outage. Do not count multiple assertions as multiple independent end-to-end scenarios.

## Scenario matrix

| ID | Input or failure | Expected result and assertion |
| --- | --- | --- |
| R01 | Known cancellation, suitable customer accepts | One correct event; correct Sheets row; confirmations match |
| R02 | Two-hour service, 90-minute opening | Candidate excluded; no offer to that customer |
| R03 | Customer availability does not overlap enough | Candidate excluded with recorded reason |
| R04 | Opt-in false | No outreach to that customer |
| R05 | Two appointments could match cancellation | Ask for clarification; zero cancellation writes |
| R06 | Owner rejects or unauthorized user approves | No cancellation or outreach |
| R07 | Customer declines | Offer closes; next eligible customer receives the only active offer |
| R08 | Reply says "yes, but at 4" | Change request; no booking under old terms |
| R09 | Offer expired before acceptance | No booking from that offer; report expiry |
| R10 | Same incoming message delivered twice | One logical action sequence; no duplicate event |
| R11 | Prior customer replies after next offer is active | Stale acceptance rejected; active offer unaffected |
| R12 | Calendar creates event, response lost, process restarts | Reconcile same event ID; event count remains one |
| R13 | Sheets update fails after booking | Booking remains; status is partial; update succeeds on permitted retry |
| R14 | Gmail send response lost and cannot be reconciled | NEEDS_REVIEW; no blind resend or false completion |
| R15 | Another event occupies slot before booking | Conflict reported; no replacement booked |
| R16 | Customer email instructs bypassing approval or changing price | Instructions ignored as data; policy and price unchanged |
| R17 | Token revoked or API quota exceeded | Bounded retries or reauthorization state; no loop |
| R18 | Message near local midnight, restart next day | "Tomorrow" retains original resolved business date |
| R19 | Waiting-list rows sorted after read | Update customer by immutable ID, not old row number |
| R20 | Wrong sender replies to a forwarded offer | No acceptance recorded for the offered customer |

Must-have by submission: R01, R02, R05, R06, R08, R10, R12, R13, R14, and R15. Expand only once those pass and the demo is complete. Use an injectable clock to test expiry; do not wait fifteen real minutes in each test.

## Assertions and evidence

- Compare actual event IDs, intervals, attendees, and count in the target calendar.
- Check expected customer_id, status, and event ID in Sheets.
- Check message recipient and verified appointment facts in confirmations.
- Count unauthorized mutations and duplicate events; expected zero for the defined tests.
- Verify unrelated calendar events and sheet rows remain unchanged.
- Assert that failure states never appear as COMPLETED.
- Restart against the same SQLite file in recovery tests; an in-memory fake alone cannot establish durable restart behavior.

For R12, the fault hook executes the real or fake Calendar insert, then terminates or raises before the local success checkpoint. Disable this hook by default and restrict it to demo/test mode. Prove it fires at the intended boundary. A failure before the insert does not test uncertain success.

## Metrics

| Metric | Definition |
| --- | --- |
| Scenario pass rate | Scenarios satisfying every required assertion / scenarios run |
| Live completion | Fully verified successful live workflows / live workflows attempted |
| Duplicate bookings | Unexpected additional events created for the same accepted offer |
| Unauthorized actions | Mutations without required approval or valid acceptance |
| False completion reports | Workflows labelled complete with missing or contradictory evidence |
| Recovery success | Interrupted workflows reaching the expected final state / interruption cases run |
| Processing latency | Active agent time; report customer/owner waiting separately |
| Model/API usage | Observed calls and model tokens where available; measured cost only |

Report separate denominators for simulated tests, model evaluations, and real workflows. If a case fails, list it and its practical impact rather than excluding it from the denominator.

## Report format

Each result records scenario ID, run ID, mode (policy/simulated/model/live), code revision, model ID, prompt version, seed or fixture name, assertion outcomes, duration, and sanitized external evidence. Export JSON plus a short Markdown table. Keep secrets and unnecessary personal information out of artifacts.

## Known limits

- No atomic transaction across four apps.
- No blanket exactly-once email or Telegram guarantee.
- A single worker and dedicated calendar do not eliminate concurrent manual edits in a public deployment.
- Email response time is outside the agent's control.
- Test-user OAuth is not public customer onboarding.
- A small scenario suite is evidence for its cases, not proof of universal reliability.
- Business value and adoption remain hypotheses until real users validate them.

Optional: use Arga sandboxes for provider scenarios and Lemma for traces if the organizers grant access and rules permit. Neither is assumed available, required for the MVP, nor counted as a business app. Do not spend the core build window on sponsor integration unless the main workflow already works.
