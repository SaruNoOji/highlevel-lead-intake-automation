# Architecture

## Components

| Component | Responsibility |
|---|---|
| Webhook | Receives a JSON lead payload and waits for an explicit response node |
| Normalize and Validate | Normalizes field names, trims strings, validates required fields and email format, and loads non-secret configuration |
| Contact lookup | Searches HighLevel by email, or by phone when email is unavailable |
| Contact branch | Reuses the existing contact ID or creates a new contact |
| Opportunity search | Retrieves opportunities for the contact in the configured pipeline |
| Exact duplicate check | Compares the External Lead ID custom-field value with the incoming ID |
| Opportunity creation | Creates an open opportunity in the configured pipeline and stage |
| Response nodes | Return `201`, `200`, `400`, or `502` with structured JSON |

## Data flow

```text
Caller
  │ POST lead payload
  ▼
n8n Webhook
  │
  ├─ invalid ───────────────────────────────► HTTP 400
  │
  ▼
HighLevel contact lookup
  │
  ├─ found ─► use contact ID
  └─ absent ► create contact ───────────────► API error path on failure
                  │
                  ▼
        opportunity search by contact
                  │
                  ├─ matching External Lead ID ─► HTTP 200
                  └─ no match ─► create opportunity ─► HTTP 201

Any handled HighLevel request error ─────────────► HTTP 502
```

## Deduplication model

Contact deduplication and opportunity deduplication solve different problems:

- Contacts are matched by email or phone so repeat customers keep one CRM identity.
- Opportunities are matched by `External Lead ID`, allowing the same contact to submit different legitimate enquiries without allowing a repeated delivery to create the same deal twice.

The HighLevel setting **Allow Multiple Opportunities per Contact** must be enabled for this model.

## Retry policy

Search operations use limited retries because GET requests are safe to repeat. Contact and opportunity creation are not retried automatically: a network timeout can happen after HighLevel creates the object but before n8n receives the response, so retrying the POST could create a duplicate.

## Known limitations

- The opportunity search retrieves up to 100 opportunities for the contact. A high-volume production account should implement pagination.
- The webhook has no caller authentication in this portfolio version.
- Errors are returned to the caller but not stored in an external audit database.
- Parallel requests carrying the same new External Lead ID can still race between search and creation. Production hardening should use a persistent idempotency store or another atomic guard.
