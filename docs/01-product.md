# Product requirements

## Problem and customer

A small salon loses a bookable slot when a customer cancels. The owner must find someone on the waiting list, check service duration and availability, exchange messages, update the calendar, and keep records consistent. SlotSaver performs that coordination.

Initial user: a solo salon owner who already uses email and a simple spreadsheet. Telegram is the owner's control channel for this prototype; customers do not need Telegram or a new app. This is a target-customer assumption to validate, not demonstrated demand. Email-based offers may be too slow for last-minute openings; focus the demo on tomorrow's appointment.

## Value proposition

"A cancellation comes in. Your receptionist fills the opening and shows you the confirmed result."

The AI interprets customer language and explains matches. Deterministic code controls eligibility, authorization, timing, and writes to apps.

## Example

1. A known customer emails: "Please cancel my appointment tomorrow at 2."
2. The agent finds the matching appointment and asks the owner on Telegram to approve releasing it and contacting the waiting list.
3. After approval, it releases that appointment and verifies the opening.
4. It reads the service catalogue and waiting list from Sheets.
5. One customer needs two hours but only 90 minutes are available; the agent explains why they do not fit.
6. The next eligible customer receives a precise offer by email: service, stylist, date, time, timezone, duration, price, and expiry.
7. The customer accepts that offer in the same conversation.
8. The agent rechecks the opening, creates the appointment, updates Sheets, sends confirmation, and reports the result to the owner.

## Scope

### Required

- Receive a cancellation from a known test customer and identify one appointment.
- Obtain explicit approval from the configured owner before cancelling and contacting customers.
- Match waiting customers against service duration, stylist, local availability, and opt-in.
- Send one outstanding offer at a time with an expiry.
- Interpret accept, decline, request-change, and unclear replies.
- Recheck eligibility and the calendar before booking.
- Persist progress and reconcile uncertain provider outcomes.
- Show links or provider IDs as evidence of completed actions.
- Support owner pause, rejection, and review of uncertain cases.

### Deferred

Payments, refunds, deposits, WhatsApp, voice calls, multiple branches, multiple workers, multi-tenant onboarding, recurring appointments, group bookings, medical scheduling, automatic discounts, travel planning, and mass outreach. No fifth app in the baseline plan. An optional later Google Drive integration could read business policies, but it must not displace reliability work.

## Matching policy

1. Exclude customers who have not opted in, are already booked, lack required data, or were already offered this opening.
2. Require the exact requested service and stylist unless the customer explicitly permitted alternatives.
3. Add configured service and cleanup duration; require the entire interval to fit both the opening and the customer's availability.
4. Order eligible customers by waiting-list timestamp, oldest first. The model does not invent a commercial priority score.
5. Contact the next eligible customer after a decline or expiry. Limit the demo workflow to three offers before asking the owner.

Store price as integer minor units with currency. For example, NGN 25,000 is 2,500,000 kobo. The agent must not change the catalogue price.

## Conversation policy

- Resolve "tomorrow" relative to the original message timestamp and business timezone, not the retry time.
- Ask when multiple appointments match or the requested date is unclear.
- A reply is acceptance only if it is from the offered customer, belongs to the active offer, is received before expiry, and clearly accepts its terms.
- "Yes, but at 4 instead" requests a change; it is not acceptance of a 2 PM appointment.
- Approval to fill the opening does not authorize discounts or unrelated calendar changes.
- Stop new work when the owner pauses. Already completed writes remain visible; do not silently undo a valid booking.

## Dashboard

One page, understandable without technical knowledge:

- Opening: date, stylist, service window, and booking value.
- Current state: awaiting owner, finding a customer, awaiting reply, booking saved, finishing updates, completed, or needs attention.
- Match explanation: why the chosen customer fits and why others do not.
- Action timeline: what actually happened, with Gmail, Calendar, and Sheets references.
- Owner actions: approve, reject, pause, and retry a known failed step.
- Evaluation panel, separate from customer-facing content: measured pass counts and scenario details.

Use "Booking saved; confirmation needs attention" when only part of the workflow has succeeded. Never display "Completed" based solely on a successful model response.

## Acceptance criteria

| ID | Requirement | Observable evidence |
| --- | --- | --- |
| P1 | Cancel only the selected, approved appointment | Target event ID and approval record; other events unchanged |
| P2 | Choose an eligible waiting customer | Service, duration, availability, opt-in, and queue position recorded |
| P3 | Require valid acceptance | Active offer ID linked to sender and reply message ID |
| P4 | Save one replacement booking | One deterministic event ID with expected attendee and interval |
| P5 | Synchronize the waiting list | Customer ID row contains confirmed status and event ID |
| P6 | Communicate verified results | Confirmation text matches Calendar; Telegram reports partial or full state accurately |
| P7 | Survive interruption | Restart completes pending steps without another event |
| P8 | Report ambiguity honestly | Review state with a specific question; no speculative write |

## Success measures

Measure completed replacement bookings, verified booking value, elapsed processing time excluding customer waiting, and observed policy violations. Claims about money earned, customer adoption, or owner time saved require separate real-world evidence.
