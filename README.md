# SlotSaver

An AI receptionist that fills cancelled appointments across Gmail, Google Calendar, Google Sheets, and Telegram.

**Pitch:** When a customer cancels, SlotSaver finds someone who wants the opening, gets their agreement, updates the schedule, and checks that the booking and confirmations agree.

**Status:** Planning and documentation only. No application, integrations, credentials, deployment, or evaluation results have been created. Prepared September 9, 2026 for the September 13 virtual hackathon. The user requested this documentation before the event; confirm eligibility of pre-event planning and any permitted preparation against the full rules.

## Read in this order

1. [Product requirements](docs/01-product.md): customer, scope, workflow, acceptance criteria.
2. [Recommended tech stack](docs/02-stack.md): technology choices, dependencies, configuration, proposed repository.
3. [Architecture and data](docs/03-architecture.md): state machine, persistence, agent boundaries, recovery.
4. [Integration setup](docs/04-integrations.md): Google and Telegram access, operations, failure handling.
5. [Reliability and evaluation](docs/05-reliability.md): scenarios, assertions, metrics, limits.
6. [One-day build plan](docs/06-build-plan.md): schedule, milestones, scope cuts, readiness checks.
7. [Demo and submission](docs/07-demo-submission.md): two-minute script and system/reliability brief template.
8. [Sources and decisions](docs/08-sources.md): official references, event facts, assumptions.

## The four apps

| App | Purpose | Visible action |
| --- | --- | --- |
| Gmail | Customer conversations | Offer an opening and send the verified booking confirmation |
| Google Calendar | Stylist availability and appointments | Release an authorized cancellation and create the replacement booking |
| Google Sheets | Waiting list and service catalogue | Update customer status and booking reference |
| Telegram | Owner control | Request approval and report the verified outcome |

The dashboard, model provider, and local database do not count as extra external apps. Four apps meet the published minimum of three; the published brief does not specify different vendors. Confirm the final rules.

## Recommended stack at a glance

- Python 3.12 or 3.13, FastAPI, Uvicorn.
- Jinja2 templates, plain CSS, and small vanilla JavaScript polling for the dashboard.
- SQLite using Python's standard library for durable workflow state.
- Google API Python client and OAuth libraries for Gmail, Calendar, and Sheets.
- Telegram Bot API through HTTPX, using long polling.
- Grok through the official xAI Python SDK, with Pydantic-validated structured output and a configurable model ID.
- pytest, HTTPX test transport, fake providers, and an injectable clock for evaluations.
- One local process and one worker for the hackathon; persistent single-instance hosting only if time and access permit.

Model access, credentials, package versions, and any costs must be checked during permitted setup. No paid account or model availability is assumed. See [stack details](docs/02-stack.md).

The user has confirmed a team build and Grok as the model provider. Team size and individual strengths are pending; the [team build plan](docs/06-build-plan.md) provides ownership options for two, three, or four people.

## MVP boundary

One salon, one stylist, one dedicated calendar, one waiting-list sheet, one owner, and one active recovery workflow at a time. Offer an opening to one customer at a time. Support one-off appointments only.

The initial business outcome is a **confirmed replacement booking**, not collected revenue. Display service price as booking value. Use synthetic customer identities and controlled inboxes in the demonstration.

## Definition of done

- All four integrations execute real operations in controlled accounts.
- One cancellation leads to an accepted, verified replacement booking.
- An interrupted run resumes without duplicating the calendar event.
- The agent asks for clarification or pauses when it cannot safely establish the next action.
- Evaluation counts and limitations are honestly reported.
- Repository, two-minute demo, and short system/reliability brief are ready before submission.

This documentation does not guarantee a prize or claim that SlotSaver is unique in the market. The competitive argument is a familiar problem, meaningful multi-app actions, and demonstrated failure recovery.
