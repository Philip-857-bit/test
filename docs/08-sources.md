# Sources, rationale, and open decisions

References checked during planning on September 9, 2026. Official documentation can change; recheck access, model availability, and final event rules before implementation. Links support the adjacent facts, not a claim that the proposed system has been built.

## Event and organizers

- [Multi-App AI Agent Hackathon](https://multiappagenthackathon.com/): published date, schedule, three-app minimum, submission assets, teams of one to four, prizes, and judging weights. The site says official rules are available before registration; the full rule text was not available in the material reviewed.
- [Arga Labs](https://www.argalabs.com/): stateful external-service twins, seeded scenarios, testing, and evidence of provider side effects.
- [Arga Labs on Y Combinator](https://www.ycombinator.com/companies/arga-labs): founders Phillip Li and Akira Tong and company focus.
- [Lemma](https://www.uselemma.ai/): production agent monitoring, instruction-based trace auditing, failure grouping, and evaluations.
- [Comma Capital](https://comma.vc/): earliest-stage investing. The user's supplied organizer post describes the founders' relationship, early investment, and invitations to judge. These relationships do not establish private judging preferences.

The recommendation to emphasize visible failure recovery is an inference from these businesses and the published 25% reliability criterion. It is not an organizer endorsement or a prediction of first place.

## Technical references

- [FastAPI templates](https://fastapi.tiangolo.com/advanced/templates/): server-rendered template integration.
- [xAI structured outputs](https://docs.x.ai/developers/model-capabilities/text/structured-outputs): Grok schema-based responses and Python/Pydantic integration. Validate business meaning separately.
- [Grok 4.6 model reference](https://docs.x.ai/developers/models/grok-4.6): one documented structured-output-capable candidate; actual model selection depends on team access, latency, and budget.
- [Google OAuth overview](https://developers.google.com/identity/protocols/oauth2): OAuth flow and testing refresh-token expiration.
- [Google restricted-scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification): testing users and production verification considerations.
- [Gmail scopes](https://developers.google.com/workspace/gmail/api/auth/scopes): scope capabilities and sensitivity.
- [Gmail sending](https://developers.google.com/workspace/gmail/api/guides/sending): encoded MIME messages and send operations.
- [Calendar event creation](https://developers.google.com/workspace/calendar/api/guides/create-events): event insertion and client-specified IDs for duplicate prevention after lost responses.
- [Calendar event resource](https://developers.google.com/workspace/calendar/api/v3/reference/events): event fields and identifiers.
- [Sheets values](https://developers.google.com/workspace/sheets/api/guides/values): read and update cell values.
- [Telegram bot introduction](https://core.telegram.org/bots): BotFather setup and user-initiated chat requirement.
- [Telegram Bot API](https://core.telegram.org/bots/api): messaging and update operations.

## Decisions recorded

| Decision | Rationale | Revisit when |
| --- | --- | --- |
| Salon cancellation recovery | Familiar outcome with multiple dependent actions | User research suggests a more accessible customer workflow |
| Four apps | Meets minimum with a separate role for each | Access fails or final rules define apps differently |
| Email customers, Telegram owner | Keeps customer experience simple while adding owner authorization | Customer communication needs a different channel |
| Python/FastAPI and simple dashboard | Small application surface for one-day build | Builder has a significantly stronger established stack |
| SQLite and one worker | Durable local execution without external database setup | Multi-instance or public deployment is required |
| Model only interprets and explains | Separates language uncertainty from write authority | New tools are needed and their policies are designed |
| Grok through the xAI SDK | Explicit user selection | User requests a provider change; model version can be chosen within Grok after testing |
| Team implementation | User confirmed teammates | Team size and skills determine workstream allocation |
| Sequential offers | Reduces competition between acceptances | Production needs higher-throughput matching |
| No payment provider | User cannot access Stripe onboarding; booking outcome needs no payments | Payment access and business need are validated later |
| Local demo baseline | Avoids relying on unverified hosting accounts | Final rules require a public project URL |

## Open questions before implementation

1. What pre-event documents, credentials, sample data, and code are permitted?
2. Do the final rules impose vendor-diversity or hosting requirements?
3. Can the controlled Google account authorize all requested scopes?
4. Which Grok model and API quota are available to the team?
5. Is Telegram accessible to the chosen demo owner?
6. How many teammates are participating, what are their strengths, and who owns each workstream?
7. Will a salon owner validate the email/waiting-list workflow and willingness to pay?

These questions do not block preparing this documentation. Access and rules do block committing to a competition implementation that depends on them.
