# Integration setup and operations

All steps below are planned setup, not completed connections. Use controlled test accounts and a dedicated calendar and spreadsheet. No Stripe account or payment processing is required.

## Google account setup

1. Create or select a Google Cloud project when event rules permit.
2. Enable Gmail API, Google Calendar API, and Google Sheets API.
3. Configure the OAuth consent screen for development/testing and add the salon demo account as a test user.
4. For the local prototype, create a Desktop OAuth client and implement a one-time installed-app authorization script with a loopback redirect. Use the server-side web application flow if later hosting OAuth onboarding.
5. Save the downloaded client configuration outside Git. Complete authorization for the controlled account and save the resulting token file outside Git.
6. Create a dedicated salon calendar and a spreadsheet with the Services and Waitlist tabs defined in [architecture](03-architecture.md).
7. Record their IDs in local configuration. Confirm read/write operations before building the workflow.

Proposed scopes:

| Scope | Purpose |
| --- | --- |
| https://www.googleapis.com/auth/gmail.readonly | Read relevant messages and verify sent messages |
| https://www.googleapis.com/auth/gmail.send | Send offers and confirmations |
| https://www.googleapis.com/auth/calendar.events | Manage appointments and inspect calendar events |
| https://www.googleapis.com/auth/spreadsheets | Read catalogue and waiting list; update booking status |

This scope set avoids Gmail label mutation. Track processed messages in SQLite. Apply application-level account, calendar, spreadsheet, sender, and recipient allowlists because OAuth scopes are broader than the demo's intended records.

Gmail read access involves restricted scopes. Google documents testing users and production verification requirements; the demo testing setup is not public production onboarding. For an external OAuth app in Testing, refresh tokens generally expire after seven days for these scopes. Recheck authorization near the demo. See [Google OAuth and Gmail sources](08-sources.md).

Do not assume that a service account can access a consumer Gmail inbox. Use user OAuth for this design. Do not publish credentials or copy token files into screenshots.

## Gmail

Read using messages.list and messages.get; send using messages.send with encoded MIME content. Poll every configured interval, with a bounded lookback overlap and provider-message deduplication. Use the dedicated inbox and controlled senders. Persist checkpoints only after messages have been recorded.

Offers must include the offer ID, exact appointment terms, expiry, and a request to reply. Preserve threading with the provider thread ID and appropriate MIME reply headers. Sender address, thread, offer ID, and parsed acceptance must all agree. A matching subject alone is insufficient.

### Uncertain sends

Gmail sends do not offer the same client-event-ID mechanism used for Calendar. Before sending, persist the operation key, recipient, content fingerprint, and a stable MIME Message-ID. After a lost response, query the sent mailbox for that identity and compare its contents before deciding what happened. Provider behavior must be tested; a MIME Message-ID is correlation evidence, not a universal idempotency guarantee.

Use bounded reconciliation attempts. If the send still cannot be established, mark NEEDS_REVIEW and do not blindly resend. This can leave a delivered message awaiting operator reconciliation; it is more honest than claiming exactly-once email delivery.

Google's sending guide documents MIME encoding and messages.send: [Gmail sending](https://developers.google.com/workspace/gmail/api/guides/sending).

## Google Calendar

Use events.list/get to inspect appointments, events.delete for the explicitly approved cancellation, and events.insert for the replacement. Re-read the original event and compare its version or fields before cancelling. Use preconditions where supported and handle changed resources as a new approval requirement.

Persist the replacement event ID before insertion. On timeout or a duplicate-ID response, get that exact event and compare expected fields. A conflicting event with the same ID is an error, not automatic success. A client-supplied ID supports recovery from lost responses. [Calendar event creation](https://developers.google.com/workspace/calendar/api/guides/create-events)

For the controlled demo, avoid automatic attendee notification duplication: use explicit Gmail messages and configure Calendar notification behavior accordingly. The appointment can still contain the intended attendee. Verify final event state, then perform the conflict check described in architecture.

Acceptance evidence: event ID, attendee, start/end, workflow marker, and observed availability. Retain the original cancellation reference. Handle an already cancelled event by reconciling the prior action; never cancel another event to make room.

## Google Sheets

Use spreadsheets.values.get for the configured ranges and values.update or values.batchUpdate for precise cell changes. Re-find customer_id before writing. Keep booking status in one source row and write the same deterministic event ID on retries. Read back the relevant cells after the update.

Prefer RAW values and do not let customer or model text become spreadsheet formulas. Do not append a fresh customer row on every retry. Treat concurrent edits or changed preferences as a reason to re-evaluate, not overwrite them silently. [Sheets cell operations](https://developers.google.com/workspace/sheets/api/guides/values)

## Telegram

1. Create the bot through BotFather's /newbot flow.
2. The owner opens the bot and sends /start; bots cannot initiate a private conversation with an arbitrary user.
3. Capture and explicitly configure the allowed numeric user ID and private chat ID.
4. Use getUpdates long polling, sendMessage for approval requests, and callback buttons for Approve, Reject, and Pause.
5. Persist updates before advancing the offset. Verify the sender, chat, workflow ID, approval scope, and current state for every callback. Reject stale or repeated approval attempts.

The Bot API token appears in request URLs. Redact URLs and HTTP logs accordingly. [Telegram introduction](https://core.telegram.org/bots), [Bot API](https://core.telegram.org/bots/api)

Store returned message IDs. Prefer editing an existing owner status message for updates once its ID is known. An initial send with a lost response can remain uncertain; do not claim exactly-once Telegram delivery. Surface the state in the local dashboard rather than repeatedly posting.

## Shared error policy

| Condition | Handling |
| --- | --- |
| 401 or revoked credentials | Pause that integration; request reauthorization |
| 403 permission error | Show missing access; do not keep retrying |
| 429 or explicit rate limit | Respect Retry-After when present; bounded backoff |
| 5xx or network failure during read | Bounded retry with backoff |
| Timeout during write | Reconcile external state before retrying |
| Invalid business data | Explain the missing or inconsistent field |
| Provider outage during finalization | Preserve the booking; expose pending steps |

Set per-request timeouts and a retry ceiling; after the ceiling, save the state for review. Redact customer bodies and credentials from logs sent to model or observability services.

## Grok setup

Grok is the selected model provider and does not count toward the four business apps. The team member owning AI setup should obtain an xAI API key, set XAI_API_KEY on the server, confirm account quota and model access, and run one Pydantic-validated cancellation interpretation and one reply interpretation. Use xai-sdk and a configurable MODEL_ID. Record the tested version and model in evaluation artifacts. The candidate grok-4.6 supports structured outputs in the current model documentation; account access still needs verification. [xAI model reference](https://docs.x.ai/developers/models/grok-4.6)

Do not put the key in browser JavaScript, Git, team-chat messages, or recordings. Share secrets through an agreed private secrets channel or manager. Customer content goes to Grok only as bounded input data; it cannot override the owner's approval or application policy. Validate meaning as well as response shape. [xAI structured outputs](https://docs.x.ai/developers/model-capabilities/text/structured-outputs)

## Integration smoke checks

In controlled accounts, prove: Gmail can read and send one message; Calendar can create/get one clearly labelled test event; Sheets can update/read one test status; Telegram can send a message and record owner approval. Also prove one structured model response validates. Clean up only the test records that the setup created. Record actual results; none have been run yet.
