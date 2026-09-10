# One-day build plan

## Event and preparation

Virtual event: Sunday, September 13, 2026. Published schedule is Pacific time: opening 9:00 AM, build 9:30 AM–4:00 PM, judging 4:00–4:40 PM, awards 4:40–5:00 PM. Teams may have one to four people. Source: [event brief](https://multiappagenthackathon.com/).

On that date, the build window corresponds to 5:30 PM September 13–12:00 AM September 14 in Africa/Lagos and Africa/Casablanca under the current timezone rules. Keep Pacific time as the source of truth and verify your calendar reminder before the event.

The full rules may define eligible preparation, app counting, model use, deployment, and submission details. This documentation is pre-event planning requested by the user. Do not imply the organizers have approved it. Check whether credentials, account setup, test data, starter code, and prior work are allowed before using them in the competition.

## Before Sunday, when permitted

- Confirm registration and capture the submission deadline and portal.
- Read the full rules; resolve preparation and hosting requirements.
- Identify the demo owner and controlled customer inboxes.
- Check Google project/OAuth and Telegram access. No account access is assumed.
- Confirm Grok API access, a structured-output model, and the team's spending limit.
- Assign named owners to the workstreams below and choose one integration lead.
- Prepare a written demo story and synthetic data design.
- Validate the recording method and microphone without creating the competition implementation prematurely.

## Sunday schedule

| Pacific time | Work | Exit condition |
| --- | --- | --- |
| 9:00–9:30 | Opening and rule review | Scope and eligibility confirmed |
| 9:30–10:30 | Scaffold, SQLite, credentials, four app smoke checks | Every app reads/writes a controlled record; model returns valid structure |
| 10:30–11:15 | Cancellation lookup and owner approval | Exactly targeted cancellation requires approved owner |
| 11:15–12:00 | Matching and outbound offer | Eligible customer receives precise offer |
| 12:00–12:30 | Acceptance and booking | Calendar and waiting list show the same booking |
| 12:30–1:30 | Durable action ledger and reconciliation | Restart after Calendar write does not duplicate event |
| 1:30–2:30 | Required evaluation cases and fixes | Honest report of pass/fail outcomes |
| 2:30–3:15 | Dashboard and demo rehearsal | User can see opening, progress, and evidence |
| 3:15–4:00 | Record, brief, repository cleanup, submit | All submission assets accessible before deadline |

This is a target allocation, not a promise that integrations will take one hour. Account access is the first go/no-go gate. Start submitting before the final minutes.

## Priority and cuts

1. Preserve a real three-or-more-app workflow, explicit acceptance, and evidence of the result.
2. Preserve the recovery scenario and honest partial states.
3. Cut styling, optional model-written prose, extra services, and extensive analytics first.
4. If Telegram cannot be connected early, move owner approval to the authenticated local dashboard and retain Gmail, Calendar, and Sheets as the three external apps. Document the reduced scope clearly.
5. If Gmail or Google OAuth cannot be connected, reconsider the workflow immediately. Do not substitute fake apps and imply compliance. A change to another provider requires new access checks and a revised demo.
6. Do not build a fifth app integration before the baseline and required tests are complete.

## Milestone checklist

- [ ] M0: Rules and access checked.
- [ ] M1: Four app operations proven.
- [ ] M2: One complete happy path with controlled customers.
- [ ] M3: Ambiguous requests and invalid acceptance stop correctly.
- [ ] M4: Interruption recovery demonstrated.
- [ ] M5: Required evaluation report generated.
- [ ] M6: Two-minute demo recorded and reviewed.
- [ ] M7: Repository, brief, and demo submitted.

## Confirmed team build

The user has confirmed a team. Member count, names, and strengths are not yet specified. Keep the one-salon MVP and use the extra capacity for earlier integration and stronger testing. Assign the four workstreams below to actual people before the build starts.

| Workstream | Owns | First deliverable | Main handoff |
| --- | --- | --- | --- |
| A: Workflow and Grok | SQLite, state machine, policy enforcement, xAI adapter | Typed cancellation/reply results and persisted workflow transitions | Validated decisions and action contracts to B/C; test hooks to D |
| B: Google integrations | Gmail, Calendar, Sheets, Google OAuth | Controlled read/write smoke checks for all three apps | Provider IDs, verification reads, and typed error outcomes to A |
| C: Owner experience | Dashboard, Telegram approval, progress timeline | Approval round trip and UI rendering a sample workflow | Approved owner event to A; actual evidence links from A/B |
| D: Reliability and submission | Fixtures, failure injection, evaluation report, recording, brief | Expected outcomes and restart-test fixture | Early failing scenarios to A/B/C; final measured evidence |

### Allocate by team size

| People | Allocation |
| --- | --- |
| 2 | Person 1: A + Google Calendar/Sheets from B. Person 2: C + Gmail from B. Both own D, with Person 2 leading the demo and Person 1 leading recovery tests. |
| 3 | Person 1: A. Person 2: B. Person 3: C and submission. All implement tests for their boundaries; Person 1 leads recovery evaluation. |
| 4 | One person per workstream A–D. |

Assign by strengths, not by table order. Name one person responsible for merging and the final submission; ownership does not mean that person writes every feature. These are human team assignments, not authorization to launch coding subagents.

### Parallel schedule and integration gates

- 9:30–9:45: all agree on schemas, configuration names, sample data, file ownership, and action-result format.
- 9:45–10:30: A builds state and Grok parsing; B verifies Google; C verifies Telegram and starts the dashboard; D writes fixtures and assertions. Combine workstreams according to team size.
- 10:30 gate: merge the first provider operations and typed model response. Resolve access failures now.
- 10:30–12:30: integrate cancellation, owner approval, matching, offer, acceptance, booking, and updates. D exercises each handoff as it lands.
- 12:30 gate: run the full workflow together against controlled real accounts. Do not let separate "working" components substitute for this shared run.
- 12:30–2:30: recovery and policy testing in parallel with UI refinement. Freeze new features at 2:30.
- 2:30–3:15: rehearse and fix only demo blockers or material correctness failures.
- 3:15–4:00: record the verified build, finish the brief, and submit. Assign one backup person to verify links and deadline completion.

### Collaboration contract

- Keep shared Pydantic schemas under one owner; agree before changing them.
- Provider operations return operation key, outcome (VERIFIED/RETRYABLE/UNKNOWN/NEEDS_REVIEW), external ID, and sanitized evidence. A owns orchestration; adapters must not silently send extra messages or create retries independently.
- Work in short branches and integrate at each gate. Agree who edits app/main.py and settings.py.
- Use a shared fixture definition but isolate each person's test calendar records, message identifiers, and database. Only the integration lead runs writes against the final demo dataset.
- Share secrets privately. Commit placeholders and sanitized evidence only.
- Report blocked dependencies immediately with the failing operation and owner. Do not wait until recording to reveal an integration is mocked.

## Proposed demo seed

Use one future local date fixed in the fixture and one stylist. The cancelled interval is 14:00–15:30. Candidate A requests 120 minutes and is excluded. Candidate B requests 60 minutes plus 15 minutes cleanup and is available 13:00–17:00; they are selected. Candidate C is available only after 16:00 and is excluded. All outreach recipients are controlled test inboxes; use explicit opt-in.

Set the eligible service price to NGN 25,000 for an illustrative booking-value display. This is fixture data, not market pricing research. Do not call it revenue collected.

## Runbook for a stalled demo

- Show the actual partial state and the failed step.
- Reauthorize if tokens expired; do not paste tokens into the recording.
- Restart using the existing database for the recovery demonstration.
- Reset to a new isolated fixture only between runs and disclose that reset.
- Use a previously recorded successful run as backup only if allowed, and label it recorded.
- Keep failed evaluation cases in the results and explain the limitation.
