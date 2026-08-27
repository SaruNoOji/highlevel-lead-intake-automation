# Security

## Repository guarantees

The public workflow export contains:

- no Private Integration Token;
- no n8n credential references;
- no real Location, Pipeline, Stage, or Custom Field IDs;
- no webhook ID;
- no pinned execution data;
- no real contact data.

## Credential handling

- Store the HighLevel token only in an n8n credential.
- Grant only the four scopes required by this workflow.
- Rotate the token immediately if it appears in a screenshot, log, exported workflow, chat message, or repository history.
- Never commit `.env` files or production workflow exports.

## Webhook protection

The portfolio workflow demonstrates input validation but does not authenticate callers. A production implementation should add one of:

- a shared secret in a request header;
- HMAC signature verification;
- an authenticated API gateway;
- IP allowlisting when the upstream source has stable addresses.

Rate limiting and payload-size limits should be enforced at the reverse proxy or API gateway.

## Personal data

Lead payloads contain personal data. Production requirements should define:

- data minimization;
- retention and deletion periods;
- access control;
- execution-log retention in n8n;
- regional and contractual privacy requirements;
- masking rules for monitoring and support logs.

## Public screenshots

Only synthetic identities are shown. Tokens, credential screens, webhook URLs, account IDs, and internal resource IDs must remain hidden.
