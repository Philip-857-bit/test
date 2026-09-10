# Recommended tech stack

## Decision

Use one Python application with a small server-rendered dashboard. This minimizes separate builds and supports durable workflow logic within a one-day event. This is a recommended stack, not a stack detected in an existing application.

| Layer | Choice | Reason |
| --- | --- | --- |
| Runtime | Python 3.12 or 3.13 | Familiar ecosystem and standard-library SQLite |
| HTTP server | FastAPI + Uvicorn | Typed endpoints and simple local serving |
| UI | Jinja2 + plain CSS + vanilla JavaScript | One dashboard, no separate frontend toolchain |
| Validation | Pydantic | Validate model decisions and provider data before use |
| Durable storage | SQLite via sqlite3 | Save events, approvals, actions, retries, and offers locally |
| Google integrations | google-api-python-client, google-auth-httplib2, google-auth-oauthlib | Official client ecosystem and OAuth |
| Owner channel | Telegram Bot API through HTTPX | Long polling avoids a public webhook dependency |
| AI | xai-sdk; configurable Grok model | User-selected provider for structured extraction and reply interpretation |
| Configuration | pydantic-settings and local environment file | Typed settings; secrets stay out of source |
| Time | datetime + zoneinfo; tzdata on Windows | Explicit IANA timezone conversion |
| Tests | pytest + HTTPX + fake adapters | Deterministic cases and integration checks |
| Observability | SQLite action ledger + redacted JSON logs | Evidence without an additional paid service |

FastAPI documents Jinja template support; xAI documents structured outputs with Pydantic and its Python SDK. Schema-valid output still needs business validation. See [sources](08-sources.md).

## Model selection

Grok is the confirmed provider. Use the official xai-sdk Python package and XAI_API_KEY. Set MODEL_ID to a Grok model available in the team's API account that supports structured output. The currently documented grok-4.6 is a candidate, subject to access, latency, and budget checks; the user has not selected a specific model version. Confirm access with a small schema-based request before implementing the workflow. Do not assume a free quota or account availability. Pin the working SDK and model configuration for the demo and record them with evaluation results.

Keep all Grok calls behind app/adapters/model.py. Request a Pydantic response with the SDK's structured-output parsing interface, then apply domain validation. Do not enable web search, X search, or code execution for this workflow; business evidence comes from the four configured apps. Set bounded timeouts and handle model errors as retryable reads or review states before any external write.

If Grok API access fails, resolve the API key, account access, or quota with the team. Do not silently switch the user-selected provider. A deterministic parser can test infrastructure but must not be presented as a working AI agent. No model-provider account has been provisioned by this documentation. See [xAI structured outputs](https://docs.x.ai/developers/model-capabilities/text/structured-outputs) and the [Grok model reference](https://docs.x.ai/developers/models/grok-4.6).

## Worker design

Run one polling worker in FastAPI's application lifespan, with SQLite as the source of pending work. It polls Gmail and Telegram, records incoming events, and advances due workflow steps. On restart, it loads unfinished work. In-memory tasks or FastAPI BackgroundTasks alone are not the durable queue.

Run one Uvicorn worker, disable automatic reload during the demo, and serialize workflow mutations. Google client calls can block; isolate them in a thread executor and do not share a non-thread-safe client instance between concurrent calls. Stop polling cleanly on shutdown. An optional later process split must preserve database claims and locks.

## Proposed repository after implementation

```text
slot saver/
  README.md
  docs/
  .env.example
  .gitignore
  requirements.txt
  app/
    main.py
    settings.py
    db.py
    worker.py
    domain/
      schemas.py
      matching.py
      workflow.py
      policies.py
    adapters/
      gmail.py
      calendar.py
      sheets.py
      telegram.py
      model.py
    templates/index.html
    static/app.css
    static/app.js
  scripts/
    authorize_google.py
    check_integrations.py
    seed_demo.py
    run_evals.py
  tests/
    fakes/
    fixtures/
    test_matching.py
    test_recovery.py
    test_policy.py
  data/                 # ignored runtime database
  secrets/              # ignored local OAuth files
  artifacts/            # sanitized evaluation and demo outputs
```

Only documentation files are supplied now. The application paths and commands below are future implementation contracts, not runnable software.

## Planned setup commands

From the project directory in PowerShell, after implementation and when event rules permit:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe scripts/authorize_google.py
.\.venv\Scripts\python.exe scripts/check_integrations.py
.\.venv\Scripts\python.exe -m pytest
.\.venv\Scripts\python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --workers 1
```

Resolve compatible package versions once, run tests, and pin those versions in requirements.txt. Do not publish untested version claims. Use the installed Python minor version if different from this example.

## Configuration contract

| Variable | Example or meaning |
| --- | --- |
| APP_ENV | demo |
| BUSINESS_TIMEZONE | Africa/Lagos |
| BUSINESS_CURRENCY | NGN |
| DATABASE_PATH | data/slotsaver.sqlite3 |
| GOOGLE_CLIENT_SECRET_PATH | secrets/google-client.json |
| GOOGLE_TOKEN_PATH | secrets/google-token.json |
| GOOGLE_CALENDAR_ID | Dedicated calendar ID from its settings |
| GOOGLE_SPREADSHEET_ID | Controlled waiting-list spreadsheet ID |
| TELEGRAM_BOT_TOKEN | Secret from BotFather |
| TELEGRAM_OWNER_USER_ID | Allowed owner's numeric user ID |
| TELEGRAM_OWNER_CHAT_ID | Allowed private chat ID |
| XAI_API_KEY | Server-side xAI API secret |
| MODEL_ID | Verified Grok model ID; grok-4.6 is a candidate to evaluate |
| POLL_INTERVAL_SECONDS | 10; apply backoff on quota errors |
| OFFER_TTL_SECONDS | 900 for the prototype; shorter only in labelled tests |
| MAX_OFFERS_PER_OPENING | 3 |
| DEMO_FAULT_INJECTION | false by default; local test/demo use only |

Ignore .env, secrets/, data/, .venv/, and raw logs in Git. Publish only a placeholder .env.example when implementing. Use fake identities and allowlisted recipients during the demo.

## Hosting

Baseline: local application with outbound polling and a recorded demonstration. Keep the machine awake and the process running while awaiting customer replies. Localhost is not accessible to remote judges; provide reproducible setup and confirm whether a hosted URL is required.

If hosting is necessary, use a single always-on instance with a persistent disk, HTTPS, and dashboard authentication. An ephemeral serverless filesystem is unsuitable for the proposed SQLite worker. Hosting availability and cost are not yet verified. Do not move to a multi-instance deployment during the build window.
