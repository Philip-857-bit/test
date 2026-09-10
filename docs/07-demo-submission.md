# Demo and submission pack

## Two-minute demo script

| Time | Screen/action | Suggested narration |
| --- | --- | --- |
| 0:00–0:12 | Cancelled appointment and waiting list | "A cancellation leaves this salon with an empty appointment. Someone on its waiting list wants that time." |
| 0:12–0:27 | Owner approves on Telegram | "SlotSaver gets the owner's permission to release it and find a replacement." |
| 0:27–0:45 | Match evidence and real Gmail offer | "This service is too long. This customer fits the time and service, so they receive an offer." |
| 0:45–1:00 | Controlled customer accepts | "The customer accepts the exact appointment. SlotSaver checks availability again." |
| 1:00–1:20 | Calendar write; labelled interruption; restart | "We deliberately interrupt it after Calendar saves the booking. On restart, it finds that booking instead of making another." |
| 1:20–1:43 | Calendar, Sheets, email, Telegram evidence | "One booking, an updated waiting list, and matching confirmations. The owner can see what actually completed." |
| 1:43–2:00 | Real evaluation summary | "We tested [actual count] scenarios, including duplicate replies, unsuitable appointments, and partial failures. [Actual passed] passed. These are the remaining limits." |

Show meaningful results at readable size; avoid spending the recording on setup or logs. If compressing customer waiting time or editing between screens, label it. The deliberate failure is locally injected, not a claim that Google failed naturally.

## Demo preparation

- Use a dedicated browser profile and hide tokens, unrelated emails, and personal information.
- Open the four relevant app views before recording.
- Verify the exact target calendar and waiting-list row.
- Keep a short record of the deterministic event ID before and after restart.
- Rehearse the fault hook and confirm it runs after the external write.
- Keep narration below two minutes and review the exported video for readable evidence.
- Display the scorecard only after real evaluation execution.

## Submission checklist

- [ ] Working repository or project link, accessible to judges.
- [ ] README with actual setup commands, dependencies, and configuration placeholders.
- [ ] Two-minute demo link tested outside the creator's session.
- [ ] Short system and reliability brief.
- [ ] Actual app list and evidence of real interactions.
- [ ] Measured evaluation results with simulated/live separation.
- [ ] Known limitations and unimplemented features disclosed.
- [ ] No secrets, raw inbox dumps, or real customer data in the repository.
- [ ] Pre-existing work and permitted preparation disclosed as required.
- [ ] Hosting or local reproduction meets final rules.
- [ ] Submission completed before the deadline.

## Short system and reliability brief template

The following is a submission template. Replace every bracketed field and rewrite implementation claims to match the finished project. Do not submit it as evidence of work completed today.

### Product

SlotSaver helps a salon fill cancelled appointments. It obtains owner approval, finds a suitable waiting customer, gets their acceptance, and synchronizes the resulting booking across [actual apps]. Our prototype supports [actual scope].

### System

The application uses [actual runtime/framework] and [actual storage]. Grok [actual model ID], accessed through [actual xAI SDK version], interprets cancellation and reply language into validated structures. Deterministic rules enforce service duration, customer availability, owner approval, active-offer identity, and price. A persisted action ledger tracks external operations and restart progress.

### Reliability

We verify external state before declaring completion. Calendar creation uses a persisted event ID so a lost response can be reconciled. Customer records are updated by stable ID. Uncertain message delivery [describe actual implemented handling]. An interrupted workflow [describe actual measured result].

### Evaluation

We ran [N] deterministic/simulated scenarios, [M] model cases, and [L] live workflows. Results: [counts and failures]. We measured [actual metrics]. The demonstration injects [specific fault boundary] and shows [observed recovery]. Evidence is available at [artifact link].

### Limitations

The prototype supports [actual limits]. Calendar and other apps are not an atomic transaction; concurrent manual edits and uncertain messaging outcomes require [actual response]. Account authorization uses [actual setup]. [List failures or incomplete features honestly.]

## Pitch and judging alignment

One sentence: "SlotSaver turns cancellations into confirmed appointments across four apps, and checks that the result is consistent."

| Published criterion | Evidence to present |
| --- | --- |
| Technical execution, 30% | Real reads/writes across apps and restart recovery |
| Reliability and evaluation, 25% | Scenario assertions, failure injection, and honest results |
| Usefulness, 20% | Clear salon workflow and confirmed replacement booking |
| Originality, 15% | Explain the specific cancellation-recovery and verification approach; do not claim first-ever invention |
| Demo clarity, 10% | Familiar story, readable external evidence, two-minute recording |

There is no separately published score for exceeding three apps. Four integrations are valuable because each contributes to the same workflow.
